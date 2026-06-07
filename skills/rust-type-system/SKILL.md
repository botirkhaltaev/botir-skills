---
name: rust-type-system
description: Deep understanding of Rust's type system including generics, traits, trait objects, associated types, GATs, and type-level programming. Use when designing generic APIs, choosing between static and dynamic dispatch, implementing complex trait bounds, or encoding invariants in types.
---

# Rust Type System

Based on Effective Rust, The Rust Reference, and The Rust Programming Language.

## When to Use This Skill

- Choosing between generics (monomorphization) and trait objects (dynamic dispatch)
- Writing complex where clauses and trait bounds
- Using associated types vs type parameters
- Understanding object safety rules
- Implementing GATs (Generic Associated Types)
- Using PhantomData for type-level programming
- Encoding invariants with the type system

## Generics vs Trait Objects

### Generics (Static Dispatch)

```rust
fn largest<T: PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0];
    for item in &list[1..] {
        if item > largest { largest = item; }
    }
    largest
}
```

- Monomorphized: separate code generated for each concrete type
- Zero runtime cost (no vtable indirection)
- Larger binary (code duplication)
- Type known at compile time

### Trait Objects (Dynamic Dispatch)

```rust
fn draw_all(shapes: &[&dyn Draw]) {
    for shape in shapes {
        shape.draw();  // vtable lookup at runtime
    }
}
```

- Single code path for all types
- Slight runtime cost (vtable pointer + indirect call)
- Smaller binary
- Type erased — heterogeneous collections possible

### Decision Matrix

| Factor | Generics | Trait Objects |
|--------|----------|--------------|
| Performance | Faster (inlined) | Indirect call overhead |
| Binary size | Larger | Smaller |
| Heterogeneous collections | No | Yes |
| Compile time | Longer | Shorter |
| Downcasting possible | No (type erased) | Via `Any` |

## Trait Bounds

### Syntax Variants

```rust
// Where clause (preferred for complex bounds)
fn process<T, U>(x: T, y: U) -> String
where
    T: Display + Clone + Send,
    U: Into<String> + Debug,
{ ... }

// Inline bounds (for simple cases)
fn print<T: Display>(val: T) { println!("{val}"); }

// impl Trait (argument position — sugar for generics)
fn process(val: impl Display + Clone) { ... }

// impl Trait (return position — opaque type)
fn make_iter() -> impl Iterator<Item = u32> { (0..10).filter(|x| x % 2 == 0) }
```

### Useful Bound Combinations

```rust
T: Send + Sync + 'static      // thread-safe, no borrows
T: Default + Clone             // can create and duplicate
T: Serialize + DeserializeOwned  // full serde support
T: AsRef<Path>                 // accepts &str, String, PathBuf, &Path
T: Into<String>                // accepts String, &str (via allocation)
```

## Associated Types vs Type Parameters

### Associated Types (one implementation per type)

```rust
trait Iterator {
    type Item;  // determined by the implementor
    fn next(&mut self) -> Option<Self::Item>;
}

impl Iterator for Counter {
    type Item = u32;  // Counter always yields u32
    fn next(&mut self) -> Option<u32> { ... }
}
```

### Type Parameters (multiple implementations per type)

```rust
trait From<T> {
    fn from(val: T) -> Self;
}

// String can implement From<&str>, From<Vec<u8>>, etc.
impl From<&str> for String { ... }
impl From<Vec<u8>> for String { ... }
```

**Rule**: Use associated types when there's one natural implementation. Use type parameters when a type could have multiple.

## Object Safety

A trait is object-safe (can be used as `dyn Trait`) if:
1. All methods have `&self` or `&mut self` receiver (not `self` or no self)
2. No methods return `Self`
3. No methods have generic type parameters
4. No associated functions (methods without self)
5. No `where Self: Sized` bounds on the trait itself

```rust
// Object-safe
trait Draw {
    fn draw(&self);
    fn bounds(&self) -> Rect;
}

// NOT object-safe (returns Self)
trait Clone {
    fn clone(&self) -> Self;
}

// Workaround: separate object-safe subset
trait CloneBox {
    fn clone_box(&self) -> Box<dyn CloneBox>;
}
```

## Generic Associated Types (GATs)

```rust
trait LendingIterator {
    type Item<'a> where Self: 'a;
    fn next(&mut self) -> Option<Self::Item<'_>>;
}

// Can return references to internal state
impl LendingIterator for WindowsMut {
    type Item<'a> = &'a mut [u8] where Self: 'a;
    fn next(&mut self) -> Option<&mut [u8]> { ... }
}
```

## PhantomData

Mark type parameters that don't appear in fields:

```rust
use std::marker::PhantomData;

struct Slice<'a, T> {
    ptr: *const T,
    len: usize,
    _lifetime: PhantomData<&'a T>,  // owns a &'a T conceptually
}

// Typestate marker
struct Authenticated;
struct Anonymous;
struct Client<State> {
    token: Option<String>,
    _state: PhantomData<State>,
}
```

## Sealed Traits

Prevent external implementations:

```rust
mod private {
    pub trait Sealed {}
}

pub trait MyTrait: private::Sealed {
    fn method(&self);
}

// Only types in this crate can implement MyTrait
pub struct MyType;
impl private::Sealed for MyType {}
impl MyTrait for MyType { fn method(&self) { } }
```

## Reference Map

- `references/generics-monomorphization.md` — generic functions, methods, impl blocks
- `references/traits-bounds.md` — trait bounds, supertraits, blanket impls, coherence
- `references/advanced-types.md` — GATs, existential types, type-level programming

## Key References

- [Effective Rust, Items 2, 12, 13](https://www.lurklurk.org/effective-rust/)
- [The Rust Reference: Traits](https://doc.rust-lang.org/reference/items/traits.html)
- [Rust API Guidelines: Type Safety](https://rust-lang.github.io/api-guidelines/)
