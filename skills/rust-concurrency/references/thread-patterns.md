# Thread Patterns & Standard Primitives

## Spawning Threads

```rust
use std::thread;

// Move closure (transfers ownership)
let data = vec![1, 2, 3];
let handle = thread::spawn(move || {
    println!("{data:?}");
});
handle.join().unwrap();  // wait for completion
```

## Scoped Threads (Rust 1.63+)

Borrow local variables without `'static`:

```rust
let mut data = vec![1, 2, 3];

thread::scope(|s| {
    s.spawn(|| { println!("{:?}", &data); });  // borrows data
    s.spawn(|| { println!("len={}", data.len()); });
});
// All scoped threads joined automatically; data is usable again
data.push(4);
```

## Arc + Mutex Pattern

The canonical shared-state pattern:

```rust
use std::sync::{Arc, Mutex};

let shared = Arc::new(Mutex::new(HashMap::new()));

let handles: Vec<_> = (0..4).map(|i| {
    let shared = Arc::clone(&shared);
    thread::spawn(move || {
        let mut map = shared.lock().unwrap();
        map.insert(i, i * 10);
    })
}).collect();

for h in handles { h.join().unwrap(); }
```

## Thread Parking

Simple signaling without condition variables:

```rust
let thread = thread::current();
let handle = thread::spawn(move || {
    // do work...
    thread.unpark();  // signal the parked thread
});
thread::park();  // block until unpark() is called
```

## Rayon for Data Parallelism

```rust
use rayon::prelude::*;

let sum: i64 = (0..1_000_000)
    .into_par_iter()  // parallel iterator
    .map(|x| x * x)
    .sum();

// Parallel sort
let mut data = vec![5, 2, 8, 1, 4];
data.par_sort();
```

## Crossbeam Channels

More flexible than `std::sync::mpsc`:

```rust
use crossbeam_channel::{bounded, select};

let (s1, r1) = bounded(10);  // bounded channel
let (s2, r2) = bounded(10);

select! {
    recv(r1) -> msg => println!("from r1: {:?}", msg),
    recv(r2) -> msg => println!("from r2: {:?}", msg),
    default => println!("nothing ready"),
}
```

## Thread Pool Pattern

```rust
use std::sync::{mpsc, Arc, Mutex};

struct ThreadPool {
    workers: Vec<thread::JoinHandle<()>>,
    sender: mpsc::Sender<Box<dyn FnOnce() + Send>>,
}

impl ThreadPool {
    fn new(size: usize) -> Self {
        let (tx, rx) = mpsc::channel();
        let rx = Arc::new(Mutex::new(rx));
        let workers = (0..size).map(|_| {
            let rx = Arc::clone(&rx);
            thread::spawn(move || {
                while let Ok(job) = rx.lock().unwrap().recv() {
                    job();
                }
            })
        }).collect();
        Self { workers, sender: tx }
    }

    fn execute(&self, f: impl FnOnce() + Send + 'static) {
        self.sender.send(Box::new(f)).unwrap();
    }
}
```

## Barrier

Synchronize N threads at a rendezvous point:

```rust
use std::sync::Barrier;

let barrier = Arc::new(Barrier::new(4));
for _ in 0..4 {
    let b = Arc::clone(&barrier);
    thread::spawn(move || {
        // phase 1
        do_work();
        b.wait();  // all threads wait here until all 4 arrive
        // phase 2 — all threads proceed together
    });
}
```

## Once / OnceLock

One-time initialization:

```rust
use std::sync::OnceLock;

static CONFIG: OnceLock<Config> = OnceLock::new();

fn get_config() -> &'static Config {
    CONFIG.get_or_init(|| load_config_from_disk())
}
```

## Common Pitfalls

1. **Deadlock from lock ordering** — always acquire multiple locks in consistent order
2. **Poisoned mutex** — `lock()` returns `Err` if a thread panicked while holding it; use `.unwrap()` or `into_inner()`
3. **Arc cycle** — `Arc<Mutex<...>>` containing another `Arc` can leak; use `Weak`
4. **Blocking in async** — never hold a `Mutex` across `.await`; use `tokio::sync::Mutex`
