# Design Patterns

## Newtype Pattern

Wrap existing types for type safety, trait implementation, or API restriction:

```rust
// Type safety
struct Meters(f64);
struct Seconds(f64);
fn speed(distance: Meters, time: Seconds) -> f64 {
    distance.0 / time.0
}

// Implement external trait on external type
struct Wrapper(Vec<String>);
impl fmt::Display for Wrapper {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "[{}]", self.0.join(", "))
    }
}

// Restrict API surface
pub struct ReadOnlyVec<T>(Vec<T>);
impl<T> ReadOnlyVec<T> {
    pub fn get(&self, i: usize) -> Option<&T> { self.0.get(i) }
    pub fn len(&self) -> usize { self.0.len() }
    // No push, pop, etc.
}
```

## Builder Pattern

For constructing complex objects with many optional parameters:

```rust
#[derive(Debug)]
pub struct Server {
    host: String,
    port: u16,
    max_connections: usize,
    tls: bool,
}

#[derive(Default)]
pub struct ServerBuilder {
    host: String,
    port: u16,
    max_connections: Option<usize>,
    tls: bool,
}

impl ServerBuilder {
    pub fn new(host: impl Into<String>, port: u16) -> Self {
        Self { host: host.into(), port, ..Default::default() }
    }

    pub fn max_connections(mut self, n: usize) -> Self {
        self.max_connections = Some(n); self
    }

    pub fn tls(mut self, enabled: bool) -> Self {
        self.tls = enabled; self
    }

    pub fn build(self) -> Server {
        Server {
            host: self.host,
            port: self.port,
            max_connections: self.max_connections.unwrap_or(100),
            tls: self.tls,
        }
    }
}

// Usage
let server = ServerBuilder::new("localhost", 8080)
    .max_connections(500)
    .tls(true)
    .build();
```

## Typestate Pattern

Encode valid state transitions in the type system:

```rust
use std::marker::PhantomData;

// States (zero-size types)
struct Draft;
struct Published;
struct Archived;

struct Post<S> {
    title: String,
    content: String,
    _state: PhantomData<S>,
}

impl Post<Draft> {
    fn new(title: String) -> Self {
        Post { title, content: String::new(), _state: PhantomData }
    }

    fn set_content(&mut self, content: String) { self.content = content; }

    fn publish(self) -> Post<Published> {
        Post { title: self.title, content: self.content, _state: PhantomData }
    }
}

impl Post<Published> {
    fn archive(self) -> Post<Archived> {
        Post { title: self.title, content: self.content, _state: PhantomData }
    }
}
// Can't call .set_content() on Published — compile error
// Can't call .archive() on Draft — compile error
```

## Command Pattern

Encapsulate operations as objects:

```rust
trait Command {
    fn execute(&self);
    fn undo(&self);
}

struct InsertText { position: usize, text: String }
impl Command for InsertText {
    fn execute(&self) { /* insert text at position */ }
    fn undo(&self) { /* remove text at position */ }
}

struct Editor {
    history: Vec<Box<dyn Command>>,
}
```

## Interpreter (Enum-Based AST)

```rust
enum Expr {
    Literal(f64),
    Add(Box<Expr>, Box<Expr>),
    Mul(Box<Expr>, Box<Expr>),
    Var(String),
}

impl Expr {
    fn eval(&self, env: &HashMap<String, f64>) -> f64 {
        match self {
            Expr::Literal(n) => *n,
            Expr::Add(a, b) => a.eval(env) + b.eval(env),
            Expr::Mul(a, b) => a.eval(env) * b.eval(env),
            Expr::Var(name) => env[name],
        }
    }
}
```

## Extension Trait

Add methods to types you don't own:

```rust
pub trait IteratorExt: Iterator {
    fn intersperse_with<F>(self, separator: F) -> IntersperseWith<Self, F>
    where
        F: FnMut() -> Self::Item,
        Self: Sized,
    { ... }
}

impl<I: Iterator> IteratorExt for I {}
```

## RAII / Scope Guard

```rust
struct ScopeGuard<F: FnOnce()> {
    callback: Option<F>,
}

impl<F: FnOnce()> Drop for ScopeGuard<F> {
    fn drop(&mut self) {
        if let Some(f) = self.callback.take() { f(); }
    }
}

fn scope_guard<F: FnOnce()>(f: F) -> ScopeGuard<F> {
    ScopeGuard { callback: Some(f) }
}

// Usage: cleanup runs even on early return or panic
let _guard = scope_guard(|| cleanup());
```
