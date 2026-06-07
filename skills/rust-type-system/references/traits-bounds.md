# Traits & Bounds

## Defining Traits

```rust
pub trait Summary {
    // Required method
    fn summarize(&self) -> String;

    // Default method (can be overridden)
    fn preview(&self) -> String {
        format!("{}...", &self.summarize()[..50])
    }
}
```

## Supertraits

Require implementors to also implement another trait:

```rust
trait Printable: Display + Debug {
    fn print(&self) { println!("{self}"); }
}
// Implementing Printable requires Display + Debug
```

## Trait Inheritance

```rust
trait Animal {
    fn name(&self) -> &str;
}

trait Pet: Animal {
    fn owner(&self) -> &str;
}

// Pet inherits Animal's requirements
struct Dog { name: String, owner: String }
impl Animal for Dog { fn name(&self) -> &str { &self.name } }
impl Pet for Dog { fn owner(&self) -> &str { &self.owner } }
```

## Coherence (Orphan Rule)

You can implement a trait for a type only if:
- You own the trait, OR
- You own the type

Cannot implement external trait for external type.

Workaround: Newtype pattern:

```rust
// Can't impl Display for Vec<T> directly
struct DisplayVec<T>(Vec<T>);
impl<T: Display> Display for DisplayVec<T> { ... }
```

## Marker Traits

Traits with no methods that indicate a property:

```rust
// Standard marker traits
unsafe trait Send {}   // safe to transfer between threads
unsafe trait Sync {}   // safe to share references between threads
trait Sized {}         // has known size at compile time
trait Unpin {}         // can be moved after being pinned
trait Copy: Clone {}   // bitwise copyable
```

## Negative Trait Bounds (Unstable)

```rust
// On nightly only
impl<T: !Copy> MyContainer<T> { ... }
```

Workaround on stable: use a sealed helper trait.

## Auto Traits

Automatically implemented unless a field doesn't implement them:

```rust
struct MySafe { data: Vec<u8> }     // auto: Send + Sync
struct MyUnsafe { ptr: *mut u8 }    // NOT Send, NOT Sync (raw pointer)
```

## Trait Objects with Multiple Traits

```rust
// Can combine auto traits with one non-auto trait
fn spawn(f: Box<dyn FnOnce() + Send + 'static>) { ... }

// Cannot combine multiple non-auto traits directly
// BAD: Box<dyn Display + Debug>  ← not allowed (two vtables needed)

// Workaround: supertrait
trait DisplayDebug: Display + Debug {}
impl<T: Display + Debug> DisplayDebug for T {}
fn log(val: &dyn DisplayDebug) { ... }
```

## where Clause Patterns

```rust
// Bounds on associated types
fn sum_values<I>(iter: I) -> i64
where
    I: Iterator,
    I::Item: Into<i64>,
{ ... }

// Higher-ranked trait bounds
fn apply<F>(f: F)
where
    F: for<'a> Fn(&'a str) -> &'a str,
{ ... }

// Bounds on the type itself
impl<T> Container<T>
where
    T: Clone + Default,
    Vec<T>: IntoIterator,
{ ... }
```

## Impl Trait in Various Positions

```rust
// Argument position (sugar for generics)
fn print(val: impl Display) { println!("{val}"); }
// Equivalent to: fn print<T: Display>(val: T)

// Return position (opaque type)
fn make_adder(x: i32) -> impl Fn(i32) -> i32 {
    move |y| x + y
}

// In trait definitions (RPITIT, Rust 1.75+)
trait Container {
    fn items(&self) -> impl Iterator<Item = &u32>;
}
```

## Extension Methods via Traits

```rust
pub trait StringExt {
    fn truncate_to(&self, max_len: usize) -> &str;
}

impl StringExt for str {
    fn truncate_to(&self, max_len: usize) -> &str {
        if self.len() <= max_len { self }
        else { &self[..self.floor_char_boundary(max_len)] }
    }
}
```
