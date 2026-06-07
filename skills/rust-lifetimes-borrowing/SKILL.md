---
name: rust-lifetimes-borrowing
description: Master Rust ownership, borrowing, and lifetimes. Use when fighting the borrow checker, annotating lifetimes, understanding move semantics, working with references across scopes, or debugging lifetime errors.
---

# Rust Lifetimes & Borrowing

Comprehensive guide to Rust's ownership system, borrowing rules, and lifetime annotations based on The Rust Programming Language, The Rust Reference, and Common Rust Lifetime Misconceptions.

## When to Use This Skill

- Borrow checker errors you don't understand
- Deciding between owned types and references
- Annotating lifetime parameters on structs/functions
- Understanding `'static`, `'a`, and elision rules
- Working with self-referential structures
- Debugging "does not live long enough" or "borrowed value dropped" errors

## Ownership Rules

1. Each value has exactly one owner.
2. When the owner goes out of scope, the value is dropped.
3. Ownership can be transferred (moved) but not duplicated (unless `Copy`).

```rust
let s1 = String::from("hello");
let s2 = s1;          // s1 is MOVED into s2; s1 is invalid
// println!("{s1}");   // compile error: use of moved value

let x: i32 = 5;
let y = x;            // i32 is Copy; both x and y are valid
```

## Borrowing Rules

1. You can have **either** one `&mut T` **or** any number of `&T` — never both simultaneously.
2. References must always be valid (no dangling).

```rust
fn calculate(data: &[u32]) -> u32 { data.iter().sum() }  // immutable borrow
fn push_item(v: &mut Vec<u32>, x: u32) { v.push(x); }   // mutable borrow
```

## Lifetime Annotations

Lifetimes describe the **scope** during which a reference is valid. They don't change how long data lives — they help the compiler verify correctness.

```rust
// Explicit: both inputs and output share lifetime 'a
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

// Struct holding a reference must declare the lifetime
struct Excerpt<'a> {
    part: &'a str,
}
```

## Lifetime Elision Rules

The compiler infers lifetimes when unambiguous:

1. Each reference parameter gets its own lifetime.
2. If there is exactly one input lifetime, it is assigned to all output lifetimes.
3. If one of the parameters is `&self` or `&mut self`, its lifetime is assigned to all output lifetimes.

```rust
// Elided (compiler infers)
fn first_word(s: &str) -> &str { ... }
// Desugared
fn first_word<'a>(s: &'a str) -> &'a str { ... }
```

## Common Misconceptions

### 1. `T` only contains owned types — WRONG

`T` is a superset of `&T` and `&mut T`. A generic `T` can be instantiated with reference types.

```rust
fn print_it<T: Display>(val: T) { println!("{val}"); }
print_it(&42);  // T = &i32 — perfectly valid
```

### 2. `T: 'static` means "lives forever" — WRONG

`T: 'static` means `T` **can** live for the entire program if needed — it contains no short-lived borrows. Owned types like `String`, `Vec<u8>` all satisfy `'static`.

```rust
fn spawn_thread<T: Send + 'static>(val: T) { ... }
spawn_thread(String::from("hi"));  // String: 'static ✓
```

### 3. `&'a T` and `T: 'a` are the same — WRONG

- `T: 'a` — T outlives 'a (T's internal references live at least as long as 'a)
- `&'a T` — a reference to T that is valid for 'a

### 4. Lifetimes grow/shrink at runtime — WRONG

Lifetimes are compile-time constructs. They describe static regions of code, not runtime durations.

### 5. Downgrading `&mut` to `&` is always safe — WRONG

Reborrowing as `&*x` is fine, but holding both the shared reborrow and the original `&mut` simultaneously is UB-adjacent (compiler rejects it).

### 6. Closures follow function elision rules — WRONG

Closures do NOT get the same elision rules as `fn` items. You often need explicit annotations or `for<'a>` bounds.

## Variance

| Type | Variance in `'a` | Variance in `T` |
|------|------------------|-----------------|
| `&'a T` | covariant | covariant |
| `&'a mut T` | covariant | **invariant** |
| `Box<T>` | — | covariant |
| `Cell<T>` | — | **invariant** |
| `fn(T) -> U` | — | **contra**variant in T, covariant in U |

Invariance means the lifetime/type cannot be shortened or lengthened — matters for mutable references and interior mutability.

## Practical Patterns

### Splitting borrows

```rust
let mut v = vec![1, 2, 3];
let (left, right) = v.split_at_mut(1);
left[0] = 10;
right[0] = 20;  // both halves borrowed mutably — safe because disjoint
```

### Returning references from methods

```rust
impl MyStruct {
    fn name(&self) -> &str { &self.name }  // ties output lifetime to &self
}
```

### NLL (Non-Lexical Lifetimes)

Borrows end at the point of last use, not at the end of the lexical scope:

```rust
let mut v = vec![1, 2, 3];
let first = &v[0];
println!("{first}");  // last use of `first`
v.push(4);            // OK — `first` borrow already ended (NLL)
```

## Reference Map

- `references/ownership-moves.md` — move semantics, Copy, Clone, drop order
- `references/borrowing-rules.md` — borrow checker mechanics, reborrowing, two-phase borrows
- `references/lifetime-annotations.md` — elision, explicit annotations, HRTB
- `references/common-misconceptions.md` — the 10 misconceptions with solutions

## Key References

- [The Rust Programming Language, Ch. 4 & 10](https://doc.rust-lang.org/book/)
- [The Rust Reference: Lifetime Elision](https://doc.rust-lang.org/reference/lifetime-elision.html)
- [Common Rust Lifetime Misconceptions](https://github.com/pretzelhammer/rust-blog/blob/master/posts/common-rust-lifetime-misconceptions.md)
