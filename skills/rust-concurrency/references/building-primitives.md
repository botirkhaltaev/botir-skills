# Building Synchronization Primitives

Based on Rust Atomics and Locks, Chapters 5-9.

## Building a Mutex

### Minimal (using futex-like wait/wake)

```rust
use std::sync::atomic::{AtomicU32, Ordering};
use std::cell::UnsafeCell;
use atomic_wait::{wait, wake_one};

pub struct Mutex<T> {
    // 0 = unlocked, 1 = locked (no waiters), 2 = locked (with waiters)
    state: AtomicU32,
    value: UnsafeCell<T>,
}

unsafe impl<T: Send> Sync for Mutex<T> {}

impl<T> Mutex<T> {
    pub const fn new(value: T) -> Self {
        Self { state: AtomicU32::new(0), value: UnsafeCell::new(value) }
    }

    pub fn lock(&self) -> MutexGuard<'_, T> {
        // Fast path: uncontended
        if self.state.compare_exchange(0, 1, Ordering::Acquire, Ordering::Relaxed).is_ok() {
            return MutexGuard { mutex: self };
        }
        // Slow path: contended
        loop {
            // Set state to 2 (locked with waiters) and wait
            if self.state.swap(2, Ordering::Acquire) == 0 {
                return MutexGuard { mutex: self };
            }
            wait(&self.state, 2);  // sleep until state != 2
        }
    }

    fn unlock(&self) {
        if self.state.swap(0, Ordering::Release) == 2 {
            wake_one(&self.state);  // wake one waiter
        }
    }
}

pub struct MutexGuard<'a, T> { mutex: &'a Mutex<T> }

impl<T> Drop for MutexGuard<'_, T> {
    fn drop(&mut self) { self.mutex.unlock(); }
}

impl<T> std::ops::Deref for MutexGuard<'_, T> {
    type Target = T;
    fn deref(&self) -> &T { unsafe { &*self.mutex.value.get() } }
}

impl<T> std::ops::DerefMut for MutexGuard<'_, T> {
    fn deref_mut(&mut self) -> &mut T { unsafe { &mut *self.mutex.value.get() } }
}
```

## Building a Channel

### One-Shot Channel

```rust
use std::sync::atomic::{AtomicBool, Ordering};
use std::cell::UnsafeCell;
use std::mem::MaybeUninit;

pub struct OneShotChannel<T> {
    message: UnsafeCell<MaybeUninit<T>>,
    ready: AtomicBool,
}

unsafe impl<T: Send> Sync for OneShotChannel<T> {}

impl<T> OneShotChannel<T> {
    pub const fn new() -> Self {
        Self {
            message: UnsafeCell::new(MaybeUninit::uninit()),
            ready: AtomicBool::new(false),
        }
    }

    /// Call exactly once from the sender side.
    pub unsafe fn send(&self, message: T) {
        (*self.message.get()).write(message);
        self.ready.store(true, Ordering::Release);
    }

    /// Returns true if message is available.
    pub fn is_ready(&self) -> bool {
        self.ready.load(Ordering::Acquire)
    }

    /// Call exactly once, only after is_ready() returns true.
    pub unsafe fn receive(&self) -> T {
        (*self.message.get()).assume_init_read()
    }
}
```

### MPSC Queue (Lock-Free)

Key idea: linked list with atomic head/tail pointers, using `compare_exchange` for concurrent push.

## Building a Condition Variable

```rust
pub struct Condvar {
    counter: AtomicU32,  // incremented on each notify
}

impl Condvar {
    pub const fn new() -> Self { Self { counter: AtomicU32::new(0) } }

    pub fn wait<'a, T>(&self, guard: MutexGuard<'a, T>) -> MutexGuard<'a, T> {
        let counter = self.counter.load(Ordering::Relaxed);
        let mutex = guard.mutex;
        drop(guard);  // release the lock
        wait(&self.counter, counter);  // sleep until counter changes
        mutex.lock()  // reacquire the lock
    }

    pub fn notify_one(&self) {
        self.counter.fetch_add(1, Ordering::Relaxed);
        wake_one(&self.counter);
    }

    pub fn notify_all(&self) {
        self.counter.fetch_add(1, Ordering::Relaxed);
        wake_all(&self.counter);
    }
}
```

## Building a RwLock

```rust
pub struct RwLock<T> {
    // u32 state: 0 = unlocked, 1..MAX-1 = N readers, MAX = write-locked
    state: AtomicU32,
    value: UnsafeCell<T>,
}

impl<T> RwLock<T> {
    pub fn read(&self) -> ReadGuard<'_, T> {
        loop {
            let s = self.state.load(Ordering::Relaxed);
            if s < u32::MAX - 1 {  // not write-locked, not overflowing
                if self.state.compare_exchange(s, s + 1, Ordering::Acquire, Ordering::Relaxed).is_ok() {
                    return ReadGuard { rwlock: self };
                }
            }
            wait(&self.state, s);
        }
    }

    pub fn write(&self) -> WriteGuard<'_, T> {
        loop {
            if self.state.compare_exchange(0, u32::MAX, Ordering::Acquire, Ordering::Relaxed).is_ok() {
                return WriteGuard { rwlock: self };
            }
            wait(&self.state, self.state.load(Ordering::Relaxed));
        }
    }
}
```

## Key Principles

1. **Start simple, optimize later** — a correct naive implementation beats a buggy "optimal" one
2. **Use Miri for testing** — `cargo +nightly miri test` catches UB in unsafe concurrent code
3. **State machine design** — model lock states as atomic integers, transitions as CAS loops
4. **Avoid thundering herd** — use `wake_one` over `wake_all` when possible
5. **Spin briefly before sleeping** — hybrid approach reduces latency for short waits
