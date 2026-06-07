---
name: rust-design-patterns
description: Idiomatic Rust design patterns, idioms, and anti-patterns. Use when structuring Rust code, choosing between patterns like Newtype/Builder/Typestate, applying idioms like mem::take or borrowed-type arguments, or avoiding common anti-patterns.
---

# Rust Design Patterns

Based on Rust Design Patterns book, Effective Rust, and community idioms.

## When to Use This Skill

- Structuring a new Rust module or crate
- Choosing the right abstraction pattern
- Avoiding common anti-patterns (Deref polymorphism, clone-to-satisfy-borrow-checker)
- Making APIs ergonomic and hard to misuse
- Encoding invariants in the type system

## Idioms

### Use Borrowed Types for Arguments

Prefer `&str` over `&String`, `&[T]` over `&Vec<T>`, `&T` over `&Box<T>`:

```rust
fn process(data: &str) { ... }  // accepts &String, &str, String slice
fn sum(nums: &[i32]) { ... }    // accepts &Vec<i32>, &[i32], arrays
```

### Default Trait

```rust
#[derive(Default)]
struct Config {
    port: u16,       // 0
    debug: bool,     // false
    name: String,    // ""
}

let cfg = Config { port: 8080, ..Config::default() };
```

### mem::take / mem::replace

Move out of a `&mut` without clone:

```rust
use std::mem;

fn extract_name(user: &mut User) -> String {
    mem::take(&mut user.name)  // leaves empty String behind
}

// Replace with specific value
let old = mem::replace(&mut self.state, State::Done);
```

### Destructuring for Clarity

```rust
let Point { x, y } = point;
let (first, rest) = slice.split_first().unwrap();

// Ignore fields
let Config { port, .. } = config;
```

### Constructor Conventions

```rust
impl Connection {
    pub fn new(addr: &str) -> Self { ... }           // simple
    pub fn builder() -> ConnectionBuilder { ... }    // complex
    pub fn with_timeout(addr: &str, ms: u64) -> Self { ... }  // variant
}
```

## Design Patterns

### Newtype

Wrap a type for type safety without runtime cost:

```rust
struct UserId(u64);
struct OrderId(u64);

// fn process(user: UserId, order: OrderId) — can't mix up arguments
```

Also used to implement external traits on external types.

### Builder

```rust
pub struct RequestBuilder {
    url: String,
    method: Method,
    headers: Vec<(String, String)>,
    timeout: Option<Duration>,
}

impl RequestBuilder {
    pub fn new(url: impl Into<String>) -> Self { ... }
    pub fn method(mut self, m: Method) -> Self { self.method = m; self }
    pub fn header(mut self, k: impl Into<String>, v: impl Into<String>) -> Self { ... }
    pub fn timeout(mut self, d: Duration) -> Self { self.timeout = Some(d); self }
    pub fn build(self) -> Result<Request, BuildError> { ... }
}
```

### Typestate

Encode state machine transitions at compile time:

```rust
struct Tcp<S> { socket: RawFd, _state: PhantomData<S> }
struct Closed;
struct Connected;
struct Listening;

impl Tcp<Closed> {
    fn connect(self, addr: &str) -> io::Result<Tcp<Connected>> { ... }
    fn listen(self, port: u16) -> io::Result<Tcp<Listening>> { ... }
}

impl Tcp<Connected> {
    fn send(&self, data: &[u8]) -> io::Result<usize> { ... }
    fn close(self) -> Tcp<Closed> { ... }
}

impl Tcp<Listening> {
    fn accept(&self) -> io::Result<Tcp<Connected>> { ... }
}
// Can't call .send() on a Closed socket — compile error
```

### Strategy (via Trait Objects or Generics)

```rust
trait Compressor { fn compress(&self, data: &[u8]) -> Vec<u8>; }

struct Pipeline { compressor: Box<dyn Compressor> }
// or with generics:
struct Pipeline<C: Compressor> { compressor: C }
```

### Visitor

```rust
trait Visitor {
    fn visit_file(&mut self, file: &File);
    fn visit_dir(&mut self, dir: &Dir);
}

trait Visitable {
    fn accept(&self, visitor: &mut dyn Visitor);
}
```

### RAII Guards

```rust
pub struct MutexGuard<'a, T> { lock: &'a Mutex<T> }

impl<T> Drop for MutexGuard<'_, T> {
    fn drop(&mut self) { self.lock.release(); }
}
// Lock released automatically when guard goes out of scope
```

## Anti-Patterns

### Deref Polymorphism

Don't use `Deref`/`DerefMut` to simulate inheritance:

```rust
// BAD: using Deref to make Child act like Parent
impl Deref for Child { type Target = Parent; ... }

// GOOD: use composition + delegation or traits
struct Child { parent: Parent }
impl Child {
    fn parent_method(&self) -> &str { self.parent.method() }
}
```

### Clone to Satisfy the Borrow Checker

```rust
// BAD: cloning just to avoid borrow conflicts
let key = map.keys().next().unwrap().clone();
map.remove(&key);

// GOOD: restructure control flow
if let Some(key) = map.keys().next().copied() {
    map.remove(&key);
}
```

### Stringly Typed APIs

```rust
// BAD
fn set_color(color: &str) { ... }

// GOOD
enum Color { Red, Green, Blue, Custom(u8, u8, u8) }
fn set_color(color: Color) { ... }
```

## Reference Map

- `references/idioms.md` — borrowed types, Default, mem::take, destructuring
- `references/patterns.md` — Newtype, Builder, Typestate, Visitor, RAII
- `references/anti-patterns.md` — Deref polymorphism, clone-borrow, stringly-typed

## Key References

- [Rust Design Patterns](https://rust-unofficial.github.io/patterns/)
- [Effective Rust](https://www.lurklurk.org/effective-rust/)
- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)
