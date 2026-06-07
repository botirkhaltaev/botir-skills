# Common Lifetime Misconceptions

Based on [pretzelhammer's blog post](https://github.com/pretzelhammer/rust-blog/blob/master/posts/common-rust-lifetime-misconceptions.md).

## 1. `T` only contains owned types

**Reality**: `T` is the set of ALL types, including `&i32`, `&mut String`, `&&Vec<u8>`, etc.

```rust
impl<T> Trait for T {}
impl<T> Trait for &T {}     // ERROR: conflicting — &T ⊂ T
impl<T> Trait for &mut T {} // ERROR: conflicting — &mut T ⊂ T
```

## 2. `T: 'static` means value lives forever

**Reality**: `T: 'static` means T **owns all its data** — it has no non-`'static` borrows inside. `String`, `Vec<u8>`, `i32` all satisfy `'static`.

```rust
// This compiles — String is 'static even though it gets dropped
fn example() {
    let s = String::from("hello");  // 'static bound satisfied
    std::thread::spawn(move || println!("{s}"));
}
```

A `&'static T` is different — that truly must point to data living for the entire program.

## 3. `&'a T` and `T: 'a` are the same

**Reality**:
- `&'a T` — a reference valid for lifetime 'a
- `T: 'a` — T's internal references all outlive 'a (T is valid if held for 'a)

`T: 'a` includes owned types (which trivially outlive anything) AND reference types whose lifetimes outlive 'a.

## 4. My code isn't generic so it doesn't have lifetimes

**Reality**: Every reference has a lifetime, even if elided:

```rust
struct Ctx { data: &str }  // ERROR: missing lifetime
struct Ctx<'a> { data: &'a str }  // required
```

## 5. If it compiles, my annotations are correct

**Reality**: Overly restrictive annotations compile but may make your API unusable:

```rust
// Too restrictive: output lifetime tied to BOTH inputs
fn first<'a>(x: &'a str, _y: &'a str) -> &'a str { x }

// Better: output only depends on x
fn first<'a>(x: &'a str, _y: &str) -> &'a str { x }
```

## 6. Boxed trait objects don't have lifetimes

**Reality**: `Box<dyn Trait>` implicitly means `Box<dyn Trait + 'static>`. For non-static:

```rust
fn process<'a>(val: Box<dyn Trait + 'a>) { ... }
```

## 7. Compiler error messages tell me how to fix my program

**Reality**: The compiler suggests adding lifetime annotations, but those suggestions may be overly conservative. Think about the actual data flow rather than blindly following suggestions.

## 8. Lifetimes grow and shrink at runtime

**Reality**: Lifetimes are purely compile-time. They map to regions of code, not runtime durations. There is no runtime cost to lifetime checking.

## 9. Downgrading `&mut` to `&` is always safe

**Reality**: While reborrowing (`&*x`) is safe, you cannot hold the shared reborrow and the original `&mut` simultaneously:

```rust
let mut val = 42;
let r = &mut val;
let shared = &*r;   // reborrow — r is "frozen"
// *r = 100;        // ERROR: r is frozen while shared exists
println!("{shared}");
*r = 100;           // OK now — shared no longer used
```

## 10. Closures follow function elision rules

**Reality**: Closures have different inference. A closure like `|s: &str| -> &str { s }` may fail because closures don't get elision rule #2. Fix with explicit annotation or helper function.

```rust
// This fails:
// let f = |s: &str| -> &str { s };

// Workaround: use a fn item or explicit for<'a>
fn identity(s: &str) -> &str { s }
let f: for<'a> fn(&'a str) -> &'a str = identity;
```
