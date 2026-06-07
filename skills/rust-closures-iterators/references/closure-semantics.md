# Closure Semantics

## Capture Rules

The compiler determines capture mode automatically (least restrictive):

1. If the closure only reads the variable → borrow by `&T` → implements `Fn`
2. If the closure mutates the variable → borrow by `&mut T` → implements `FnMut`
3. If the closure moves/drops the variable → take by `T` → implements `FnOnce`

```rust
let s = String::from("hello");

let f1 = || println!("{s}");          // captures &s → Fn
let f2 = || { let _ = s; };          // captures s (move) → FnOnce
let mut v = vec![];
let f3 = || v.push(1);               // captures &mut v → FnMut
```

## move Keyword

Forces all captures by value, regardless of how they're used:

```rust
let name = String::from("Alice");
let f = move || println!("{name}");
// name is moved into the closure even though only &name is needed
// drop(name);  // ERROR: name was moved
```

Use `move` when:
- Spawning threads (closure must be `'static`)
- Returning closures from functions
- Async blocks (avoid lifetime issues)

## Fn Trait Implementations

```rust
// Implements all three traits (only reads)
let add = |a: i32, b: i32| a + b;
fn takes_fn(f: impl Fn(i32, i32) -> i32) { f(1, 2); }
fn takes_fnmut(f: impl FnMut(i32, i32) -> i32) { f(1, 2); }
fn takes_fnonce(f: impl FnOnce(i32, i32) -> i32) { f(1, 2); }
// All three work with `add`

// Implements FnMut and FnOnce (mutates)
let mut count = 0;
let mut increment = || { count += 1; count };
// Cannot pass to Fn, but can pass to FnMut and FnOnce

// Implements only FnOnce (consumes)
let data = vec![1, 2, 3];
let consume = || { drop(data); };
// Can only be called once
```

## Higher-Order Functions

Functions that take or return closures:

```rust
// Taking a closure
fn apply_twice<F: Fn(i32) -> i32>(f: F, x: i32) -> i32 {
    f(f(x))
}

// Returning a closure
fn multiplier(factor: i32) -> impl Fn(i32) -> i32 {
    move |x| x * factor
}

// Closure as field
struct EventHandler {
    on_click: Box<dyn FnMut(Event)>,
}
```

## Closure Size

Closures are anonymous structs holding their captures:

```rust
let x: i32 = 5;          // 4 bytes
let f = || x + 1;        // captures x → closure is ~4 bytes
                          // (size of captures)

let s = String::from("hi");  // 24 bytes (ptr, len, cap)
let g = move || s.len();     // closure is ~24 bytes

// Zero-capture closures are zero-sized
let h = || 42;               // 0 bytes
```

## Function Pointers vs Closures

```rust
// Function pointer (no captures)
fn add_one(x: i32) -> i32 { x + 1 }
let fp: fn(i32) -> i32 = add_one;

// Function pointers implement Fn, FnMut, FnOnce
fn apply(f: fn(i32) -> i32, x: i32) -> i32 { f(x) }

// Non-capturing closures can coerce to function pointers
let fp: fn(i32) -> i32 = |x| x + 1;  // OK: no captures
```

## Common Patterns

### Callbacks

```rust
struct Button {
    on_click: Option<Box<dyn FnMut()>>,
}

impl Button {
    fn set_on_click(&mut self, f: impl FnMut() + 'static) {
        self.on_click = Some(Box::new(f));
    }

    fn click(&mut self) {
        if let Some(ref mut f) = self.on_click { f(); }
    }
}
```

### Compose Functions

```rust
fn compose<A, B, C>(f: impl Fn(A) -> B, g: impl Fn(B) -> C) -> impl Fn(A) -> C {
    move |x| g(f(x))
}

let double_then_add = compose(|x: i32| x * 2, |x| x + 1);
assert_eq!(double_then_add(3), 7);
```

### Memoization

```rust
use std::collections::HashMap;

fn memoize<A: Eq + Hash + Clone, B: Clone>(
    f: impl Fn(A) -> B,
) -> impl FnMut(A) -> B {
    let mut cache = HashMap::new();
    move |arg| {
        cache.entry(arg.clone()).or_insert_with(|| f(arg)).clone()
    }
}
```
