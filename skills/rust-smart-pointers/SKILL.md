---
name: rust-smart-pointers
description: Understand and use Rust smart pointers including Box, Rc, Arc, RefCell, Cell, Cow, and Pin. Use when choosing heap allocation strategies, implementing shared ownership, interior mutability patterns, or working with self-referential types.
---

# Rust Smart Pointers

Based on The Rust Programming Language Ch. 15, Effective Rust, and the Nomicon.

## When to Use This Skill

- Choosing between stack and heap allocation
- Implementing shared ownership (`Rc`/`Arc`)
- Interior mutability (`Cell`/`RefCell`)
- Copy-on-write patterns (`Cow`)
- Pinning for self-referential types (`Pin`)
- Understanding `Deref` coercions

## Smart Pointer Overview

| Type | Ownership | Thread-safe | Mutability | Use Case |
|------|-----------|-------------|------------|----------|
| `Box<T>` | Single | Yes | Via `&mut` | Heap allocation, recursive types |
| `Rc<T>` | Shared | No | Immutable | Single-thread shared ownership |
| `Arc<T>` | Shared | Yes | Immutable | Multi-thread shared ownership |
| `Cell<T>` | Single | No | Interior | Copy types, no-borrow mutation |
| `RefCell<T>` | Single | No | Interior | Runtime borrow checking |
| `Mutex<T>` | Shared | Yes | Interior | Thread-safe mutable access |
| `RwLock<T>` | Shared | Yes | Interior | Multi-reader, single-writer |
| `Cow<'a, T>` | Borrowed or Owned | Depends | Clone-on-write | Avoid unnecessary copies |
| `Pin<P>` | Same as P | Same as P | Same as P | Prevent moves (async, self-ref) |

## Box<T>

Heap allocation with single ownership:

```rust
// Recursive type (must have known size)
enum List {
    Cons(i32, Box<List>),
    Nil,
}

// Large stack values
let big_array = Box::new([0u8; 1_000_000]);

// Trait objects
let drawable: Box<dyn Draw> = Box::new(Circle { radius: 5.0 });

// Transfer ownership without copy
fn process(data: Box<[u8]>) { ... }
```

## Rc<T> (Reference Counted)

Shared ownership, single-threaded:

```rust
use std::rc::Rc;

let shared = Rc::new(vec![1, 2, 3]);
let clone1 = Rc::clone(&shared);  // increment ref count (cheap)
let clone2 = Rc::clone(&shared);

assert_eq!(Rc::strong_count(&shared), 3);
// Dropped when count reaches 0
```

### Weak References (Break Cycles)

```rust
use std::rc::{Rc, Weak};

struct Node {
    children: Vec<Rc<Node>>,
    parent: Weak<Node>,       // weak to prevent cycle
}

let parent = Rc::new(Node { ... });
let weak_ref: Weak<Node> = Rc::downgrade(&parent);

// Upgrading: Weak → Option<Rc>
if let Some(strong) = weak_ref.upgrade() {
    // parent still alive
}
```

## Arc<T> (Atomic Reference Counted)

Thread-safe `Rc`:

```rust
use std::sync::Arc;

let data = Arc::new(vec![1, 2, 3]);

let handles: Vec<_> = (0..4).map(|_| {
    let data = Arc::clone(&data);
    std::thread::spawn(move || {
        println!("{:?}", data);
    })
}).collect();
```

Typical pattern: `Arc<Mutex<T>>` for shared mutable state across threads.

## Cell<T>

Interior mutability for `Copy` types (no borrow tracking):

```rust
use std::cell::Cell;

struct Counter {
    count: Cell<u32>,  // can mutate through &self
}

impl Counter {
    fn increment(&self) {  // note: &self, not &mut self
        self.count.set(self.count.get() + 1);
    }
}
```

## RefCell<T>

Runtime-checked borrow rules:

```rust
use std::cell::RefCell;

let data = RefCell::new(vec![1, 2, 3]);

// Immutable borrow
let borrowed = data.borrow();      // panics if mutably borrowed
println!("{:?}", *borrowed);
drop(borrowed);

// Mutable borrow
let mut mut_borrowed = data.borrow_mut();  // panics if any borrow exists
mut_borrowed.push(4);
```

Common pattern: `Rc<RefCell<T>>` for shared mutable ownership.

## Cow<'a, T> (Clone on Write)

Avoids allocation when mutation isn't needed:

```rust
use std::borrow::Cow;

fn ensure_lowercase(input: &str) -> Cow<'_, str> {
    if input.chars().all(|c| c.is_lowercase()) {
        Cow::Borrowed(input)      // no allocation
    } else {
        Cow::Owned(input.to_lowercase())  // allocates only when needed
    }
}

// Cow implements Deref, so it works like &str
let result = ensure_lowercase("Hello");
println!("{result}");  // via Deref to &str
```

## Pin<P>

Guarantees that the pointed-to value won't be moved in memory:

```rust
use std::pin::Pin;

// Pin<Box<T>> — heap-pinned value
let pinned: Pin<Box<MyFuture>> = Box::pin(MyFuture::new());

// Pin<&mut T> — stack-pinned value
let mut val = MyFuture::new();
let pinned = Pin::new(&mut val);  // only works if T: Unpin

// For !Unpin types, use pin! macro (Rust 1.68+)
use std::pin::pin;
let pinned = pin!(MyFuture::new());
```

### When Pin Matters

- Async futures (self-referential across `.await` points)
- Intrusive data structures (nodes point to themselves)
- Any type that stores pointers to its own fields

## Deref Coercions

```rust
// Smart pointers implement Deref, enabling automatic coercion:
// &Box<T>     → &T
// &String     → &str
// &Vec<T>     → &[T]
// &Arc<T>     → &T
// &Rc<T>      → &T
// &Cow<'_, T> → &T

fn takes_str(s: &str) { ... }
let boxed = Box::new(String::from("hello"));
takes_str(&boxed);  // Box<String> → String → str (two derefs)
```

## Reference Map

- `references/box-rc-arc.md` — heap allocation, shared ownership, weak references
- `references/interior-mutability.md` — Cell, RefCell, Mutex, patterns
- `references/pin-cow.md` — Pin mechanics, Cow patterns, Deref

## Key References

- [The Rust Programming Language, Ch. 15](https://doc.rust-lang.org/book/ch15-00-smart-pointers.html)
- [std::pin documentation](https://doc.rust-lang.org/std/pin/)
- [The Rustonomicon: Subtyping](https://doc.rust-lang.org/nomicon/subtyping.html)
