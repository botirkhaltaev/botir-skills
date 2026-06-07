---
name: rust-concurrency
description: Low-level concurrency in Rust including atomics, memory ordering, building locks/channels, Send/Sync, and thread synchronization. Use when implementing lock-free data structures, understanding memory ordering, building synchronization primitives, or debugging race conditions.
---

# Rust Concurrency & Atomics

Comprehensive guide based on Rust Atomics and Locks by Mara Bos and The Rust Programming Language Ch. 16.

## When to Use This Skill

- Implementing lock-free or wait-free data structures
- Understanding `Ordering` (Relaxed, Acquire, Release, AcqRel, SeqCst)
- Building custom mutexes, channels, or condition variables
- Debugging data races or incorrect synchronization
- Understanding `Send`, `Sync`, and thread safety markers
- Using `Arc<Mutex<T>>` and `Arc<RwLock<T>>` patterns
- Working with `std::sync::atomic` types

## Thread Safety Markers

### Send

A type is `Send` if it can be transferred to another thread. Most types are `Send`. Notable exceptions: `Rc<T>`, raw pointers, `MutexGuard` (on some platforms).

### Sync

A type is `Sync` if `&T` is `Send` (safe to share references across threads). Examples: `Mutex<T>` is `Sync` (even though `T` isn't necessarily), `Cell<T>` is NOT `Sync`.

```rust
// Auto-derived by compiler. Manually implement (unsafe) only when wrapping raw pointers:
unsafe impl Send for MyWrapper {}
unsafe impl Sync for MyWrapper {}
```

## Atomic Operations

### Types

`AtomicBool`, `AtomicI8`..`AtomicI64`, `AtomicU8`..`AtomicU64`, `AtomicUsize`, `AtomicPtr<T>`

### Core Operations

```rust
use std::sync::atomic::{AtomicU64, Ordering};

let counter = AtomicU64::new(0);

counter.store(42, Ordering::Release);           // write
let val = counter.load(Ordering::Acquire);      // read
let old = counter.swap(100, Ordering::AcqRel);  // exchange

// Fetch-and-modify (returns OLD value)
counter.fetch_add(1, Ordering::Relaxed);
counter.fetch_sub(1, Ordering::Relaxed);
counter.fetch_or(mask, Ordering::Relaxed);
counter.fetch_and(!mask, Ordering::Relaxed);

// Compare-and-swap
let result = counter.compare_exchange(
    expected,        // current expected value
    new_value,       // value to store if current == expected
    Ordering::AcqRel,  // ordering on success
    Ordering::Acquire,  // ordering on failure
);
// Returns Ok(old) on success, Err(actual) on failure
```

## Memory Ordering

| Ordering | Guarantees |
|----------|-----------|
| `Relaxed` | Atomicity only; no ordering with other operations |
| `Acquire` | Loads: all subsequent reads/writes see effects before the paired Release |
| `Release` | Stores: all prior reads/writes are visible to the paired Acquire |
| `AcqRel` | Both Acquire and Release (for read-modify-write ops) |
| `SeqCst` | Total global ordering; all threads see the same order |

### Acquire-Release Pattern

```rust
// Thread A (producer)
DATA.store(value, Ordering::Relaxed);     // write data
READY.store(true, Ordering::Release);      // publish flag

// Thread B (consumer)
while !READY.load(Ordering::Acquire) {}    // wait for flag
let val = DATA.load(Ordering::Relaxed);    // guaranteed to see value
```

### When to Use What

- **Relaxed**: Simple counters, statistics, progress indicators
- **Acquire/Release**: Publish data between threads (producer-consumer)
- **SeqCst**: When you need total ordering (rare; usually overkill)

## Building a SpinLock

```rust
use std::sync::atomic::{AtomicBool, Ordering};
use std::cell::UnsafeCell;

pub struct SpinLock<T> {
    locked: AtomicBool,
    value: UnsafeCell<T>,
}

unsafe impl<T: Send> Sync for SpinLock<T> {}

impl<T> SpinLock<T> {
    pub fn lock(&self) -> &mut T {
        while self.locked.swap(true, Ordering::Acquire) {
            std::hint::spin_loop();  // CPU hint: we're spinning
        }
        unsafe { &mut *self.value.get() }
    }

    pub fn unlock(&self) {
        self.locked.store(false, Ordering::Release);
    }
}
```

## Standard Library Primitives

### Mutex<T>

```rust
use std::sync::{Arc, Mutex};

let data = Arc::new(Mutex::new(vec![]));
let data_clone = Arc::clone(&data);

std::thread::spawn(move || {
    let mut lock = data_clone.lock().unwrap();
    lock.push(42);
});  // MutexGuard dropped → lock released
```

### RwLock<T>

```rust
use std::sync::RwLock;

let lock = RwLock::new(HashMap::new());
// Multiple readers simultaneously
let r = lock.read().unwrap();
// Exclusive writer
let mut w = lock.write().unwrap();
```

### Channels (mpsc)

```rust
use std::sync::mpsc;

let (tx, rx) = mpsc::channel();
let tx2 = tx.clone();  // multiple producers

std::thread::spawn(move || tx.send("hello").unwrap());
std::thread::spawn(move || tx2.send("world").unwrap());

for msg in rx { println!("{msg}"); }
```

## Reference Map

- `references/atomics-ordering.md` — atomic types, operations, memory ordering in depth
- `references/building-primitives.md` — implementing Mutex, Channel, Condvar from atomics
- `references/thread-patterns.md` — Arc+Mutex, scoped threads, thread pools, parking

## Key References

- [Rust Atomics and Locks](https://marabos.nl/atomics/) by Mara Bos
- [The Rust Programming Language, Ch. 16](https://doc.rust-lang.org/book/ch16-00-concurrency.html)
- [std::sync::atomic](https://doc.rust-lang.org/std/sync/atomic/)
