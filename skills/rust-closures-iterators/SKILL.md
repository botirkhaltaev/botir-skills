---
name: rust-closures-iterators
description: Master closures and iterators in Rust. Use when writing functional-style code, implementing custom iterators, using iterator adaptors, understanding Fn/FnMut/FnOnce traits, or optimizing iterator chains.
---

# Closures & Iterators

Based on The Rust Programming Language Ch. 13, Effective Rust, and the Iterator trait documentation.

## When to Use This Skill

- Writing closures with correct capture semantics
- Understanding `Fn`, `FnMut`, `FnOnce` trait hierarchy
- Chaining iterator adaptors for data transformation
- Implementing `Iterator` for custom types
- Using `collect()` effectively
- Performance of iterator chains vs loops

## Closures

### Capture Modes

```rust
let name = String::from("Alice");
let numbers = vec![1, 2, 3];

// Borrow (&T) — Fn
let print = || println!("{name}");      // captures &name

// Mutable borrow (&mut T) — FnMut
let mut acc = 0;
let mut add = |x| { acc += x; };       // captures &mut acc

// Move (T) — FnOnce
let consume = move || drop(numbers);    // takes ownership of numbers
```

### Fn Trait Hierarchy

```
FnOnce   (can be called once — may consume captured values)
  ↑
FnMut    (can be called multiple times — may mutate captures)
  ↑
Fn       (can be called multiple times — only reads captures)
```

Every `Fn` is also `FnMut` and `FnOnce`. Compiler picks the least restrictive trait.

### Closure Types in APIs

```rust
// Accept any callable (most flexible for callers)
fn apply<F: FnOnce() -> T, T>(f: F) -> T { f() }

// Accept reusable callable
fn repeat<F: Fn() -> T, T>(f: F, n: usize) -> Vec<T> {
    (0..n).map(|_| f()).collect()
}

// Accept mutating callable
fn fold<F: FnMut(Acc, Item) -> Acc>(mut f: F, init: Acc, items: &[Item]) -> Acc { ... }
```

### move Closures

Force ownership transfer (required for threads, async):

```rust
let data = vec![1, 2, 3];
let handle = std::thread::spawn(move || {
    println!("{data:?}");  // data moved into closure
});
```

### Returning Closures

```rust
fn make_adder(x: i32) -> impl Fn(i32) -> i32 {
    move |y| x + y
}

// Dynamic dispatch (multiple possible closures)
fn get_transform(kind: &str) -> Box<dyn Fn(i32) -> i32> {
    match kind {
        "double" => Box::new(|x| x * 2),
        "negate" => Box::new(|x| -x),
        _ => Box::new(|x| x),
    }
}
```

## Iterators

### The Iterator Trait

```rust
trait Iterator {
    type Item;
    fn next(&mut self) -> Option<Self::Item>;
    // 75+ provided methods (map, filter, fold, collect, etc.)
}
```

### Creating Iterators

```rust
let v = vec![1, 2, 3];
v.iter()        // yields &i32
v.iter_mut()    // yields &mut i32
v.into_iter()   // yields i32 (consumes v)

// Ranges
(0..10)         // 0 to 9
(0..=10)        // 0 to 10
('a'..='z')     // char range
```

### Key Adaptors (Lazy — No Work Until Consumed)

```rust
let result: Vec<_> = items.iter()
    .filter(|x| x.is_valid())           // keep matching elements
    .map(|x| x.transform())             // transform each element
    .take(10)                            // limit to first 10
    .skip(2)                             // skip first 2
    .enumerate()                         // add index: (i, item)
    .flat_map(|(i, x)| x.children())    // flatten nested iterators
    .chain(extra.iter())                 // append another iterator
    .zip(other.iter())                   // pair with another iterator
    .inspect(|x| println!("{x:?}"))     // debug peek (no modification)
    .collect();
```

### Consuming Methods (Drive the Iterator)

```rust
iter.count()                // number of elements
iter.sum::<i32>()           // sum all elements
iter.product::<i32>()       // multiply all elements
iter.min() / iter.max()     // find extremes
iter.any(|x| predicate(x))  // short-circuit OR
iter.all(|x| predicate(x))  // short-circuit AND
iter.find(|x| x.id == 5)   // first matching element
iter.position(|x| x > 10)  // index of first match
iter.fold(init, |acc, x| acc + x)  // general reduction
iter.for_each(|x| process(x))      // side effects
```

### collect() Power

```rust
// Into Vec
let v: Vec<_> = iter.collect();

// Into HashMap
let map: HashMap<_, _> = pairs.into_iter().collect();

// Into String
let s: String = chars.collect();

// Into Result<Vec<T>, E>
let results: Result<Vec<_>, _> = items.iter().map(parse).collect();
```

## Custom Iterator

```rust
struct Fibonacci { a: u64, b: u64 }

impl Fibonacci {
    fn new() -> Self { Self { a: 0, b: 1 } }
}

impl Iterator for Fibonacci {
    type Item = u64;
    fn next(&mut self) -> Option<u64> {
        let result = self.a;
        let new_b = self.a + self.b;
        self.a = self.b;
        self.b = new_b;
        Some(result)
    }
}

// Usage
let first_10: Vec<_> = Fibonacci::new().take(10).collect();
```

## Performance

Iterator chains are **zero-cost abstractions** — they compile to the same assembly as hand-written loops. The compiler fuses adaptors into a single pass (no intermediate allocations).

```rust
// These generate identical machine code:
let sum: i32 = v.iter().filter(|&&x| x > 0).sum();

let mut sum = 0;
for &x in &v { if x > 0 { sum += x; } }
```

## Reference Map

- `references/closure-semantics.md` — capture modes, Fn traits, move, higher-order functions
- `references/iterator-adaptors.md` — all adaptors with examples
- `references/custom-iterators.md` — implementing Iterator, IntoIterator, DoubleEndedIterator

## Key References

- [The Rust Programming Language, Ch. 13](https://doc.rust-lang.org/book/ch13-00-functional-features.html)
- [std::iter documentation](https://doc.rust-lang.org/std/iter/)
- [Effective Rust: Iterators](https://www.lurklurk.org/effective-rust/iterators.html)
