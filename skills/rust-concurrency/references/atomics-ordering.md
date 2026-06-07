# Atomics & Memory Ordering

## Why Atomics Exist

Without atomics, concurrent reads/writes to the same variable are undefined behavior. Atomics provide:
1. **Atomicity** — operations are indivisible
2. **Ordering** — control over what other operations are visible

## The Hardware Reality

Modern CPUs have:
- Store buffers (writes delayed)
- Cache hierarchies (cores see different data)
- Out-of-order execution

Memory ordering constrains how the CPU and compiler may reorder operations.

## Relaxed Ordering

Only guarantees atomicity. No ordering relationship with other variables.

```rust
use std::sync::atomic::{AtomicUsize, Ordering::Relaxed};

static COUNTER: AtomicUsize = AtomicUsize::new(0);

// Safe: no data depends on the counter's relationship to other variables
fn increment() { COUNTER.fetch_add(1, Relaxed); }
fn get_count() -> usize { COUNTER.load(Relaxed) }
```

Use for: statistics, progress bars, counters where exact ordering doesn't matter.

## Acquire-Release

Creates a **happens-before** relationship between a Release store and an Acquire load on the same atomic variable.

```rust
static DATA: AtomicU64 = AtomicU64::new(0);
static READY: AtomicBool = AtomicBool::new(false);

// Producer thread
fn produce() {
    DATA.store(42, Ordering::Relaxed);       // (1) write data
    READY.store(true, Ordering::Release);     // (2) release: all prior writes visible
}

// Consumer thread
fn consume() -> u64 {
    while !READY.load(Ordering::Acquire) {}   // (3) acquire: sees (2)
    DATA.load(Ordering::Relaxed)              // (4) guaranteed to see (1) = 42
}
```

The Release at (2) synchronizes-with the Acquire at (3), establishing:
- Everything before (2) happens-before everything after (3)

## AcqRel (Acquire + Release)

For read-modify-write operations that both read AND publish:

```rust
// Compare-exchange that both acquires other threads' writes
// and releases our writes to other threads
val.compare_exchange(old, new, Ordering::AcqRel, Ordering::Acquire);
```

## SeqCst (Sequentially Consistent)

Strongest ordering. All SeqCst operations appear in a single total order agreed upon by all threads.

```rust
// Rarely needed. Use when:
// - Multiple atomic variables must appear to change in a consistent global order
// - You can't reason about correctness with weaker orderings
A.store(1, Ordering::SeqCst);
B.store(1, Ordering::SeqCst);
// All threads agree on whether A or B was stored first
```

## Fences

Standalone ordering barriers not tied to a specific atomic:

```rust
use std::sync::atomic::fence;

// Equivalent to an Acquire load (when paired with a preceding atomic load)
let val = x.load(Ordering::Relaxed);
fence(Ordering::Acquire);
// Everything after the fence sees everything before the paired Release

// Equivalent to a Release store
fence(Ordering::Release);
x.store(val, Ordering::Relaxed);
```

## compare_exchange vs compare_exchange_weak

```rust
// Strong: guaranteed to succeed if current == expected
val.compare_exchange(expected, new, success_ord, failure_ord);

// Weak: may spuriously fail even if current == expected (faster on ARM)
// Use in loops where you'll retry anyway
loop {
    let current = val.load(Ordering::Relaxed);
    match val.compare_exchange_weak(current, current + 1, Ordering::AcqRel, Ordering::Relaxed) {
        Ok(_) => break,
        Err(actual) => { /* retry with actual */ }
    }
}
```

## Platform Differences

- **x86/x64**: Strong memory model. Loads have implicit Acquire, stores have implicit Release. Relaxed and Acquire/Release compile to the same instructions. Only SeqCst adds extra fences.
- **ARM/RISC-V**: Weak memory model. Each ordering level generates different instructions. Relaxed is genuinely cheaper than Acquire/Release.

## Common Mistakes

1. Using `Relaxed` when data dependencies exist between atomics
2. Using `SeqCst` everywhere "to be safe" (correct but slow on ARM)
3. Forgetting that `compare_exchange` has TWO orderings (success and failure)
4. Assuming atomic operations on different variables are ordered (they aren't without fences)
