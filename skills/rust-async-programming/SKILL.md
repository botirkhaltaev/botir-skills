---
name: rust-async-programming
description: Master async/await programming in Rust. Use when writing async functions, working with Futures, using async runtimes like tokio, handling streams, implementing select/join patterns, understanding Pin/Unpin, building executors, or debugging async code.
---

# Async Programming in Rust

Comprehensive guide to async/await based on Asynchronous Programming in Rust, tokio docs, and the Pin/Unpin model.

## When to Use This Skill

- Writing async/await code
- Understanding Futures, executors, and wakers
- Using tokio or async-std runtimes
- Working with async streams
- Implementing concurrent operations with select/join
- Understanding Pin/Unpin and why futures need pinning
- Cancellation safety and structured concurrency
- Handling async errors and timeouts

## Core References

- [Asynchronous Programming in Rust](https://rust-lang.github.io/async-book/)
- [tokio](https://tokio.rs) - Popular async runtime
- [Pin documentation](https://doc.rust-lang.org/std/pin/)

## async/await Basics

### Async Functions

```rust
async fn fetch_data() -> Result<String, reqwest::Error> {
    let response = reqwest::get("https://example.com").await?;
    let body = response.text().await?;
    Ok(body)
}
```

An `async fn` returns an `impl Future<Output = T>`. No work happens until the future is `.await`ed or polled.

### The .await Operator

```rust
async fn example() {
    let result = some_future.await;
    // Suspends here; resumes when the future completes
}
```

Each `.await` point is a potential suspension — the executor can run other tasks while waiting.

## Futures Deep Dive

### The Future Trait

```rust
trait Future {
    type Output;
    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output>;
}

enum Poll<T> {
    Ready(T),    // future completed with value
    Pending,     // not yet ready; will call waker when ready
}
```

### How Polling Works

1. Executor calls `poll()` on the future
2. If `Ready(val)` → done, return value
3. If `Pending` → future stores the `Waker` from `cx`
4. When the underlying I/O is ready, the `Waker` is called
5. Executor re-polls the future

### State Machine Transformation

```rust
// This async fn:
async fn example() {
    let a = step_one().await;
    let b = step_two(a).await;
    a + b
}

// Becomes roughly:
enum ExampleFuture {
    Step1 { fut: StepOneFuture },
    Step2 { a: i32, fut: StepTwoFuture },
    Done,
}
```

## Pin/Unpin Explained

### Why Futures Need Pinning

Async state machines may contain self-references (a reference to a local variable stored in the same struct). If the struct moves in memory, those references dangle.

```rust
async fn self_referential() {
    let data = vec![1, 2, 3];
    let reference = &data;     // reference points to data
    some_async_op().await;     // future may be moved between .awaits!
    println!("{reference:?}"); // if moved, reference is dangling
}
```

`Pin<&mut Self>` guarantees the future won't move after first poll.

### Unpin: Safe to Move

Most types implement `Unpin` (safe to move even when pinned). Only compiler-generated futures and types with `PhantomPinned` are `!Unpin`.

```rust
// These are Unpin — pinning has no effect
let x: Pin<&mut i32> = Pin::new(&mut 42);  // fine
let s: Pin<&mut String> = Pin::new(&mut String::new());  // fine

// Async futures are !Unpin — must use Box::pin or pin! macro
let fut = Box::pin(my_async_fn());  // Pin<Box<dyn Future>>
```

## Async Runtimes

### tokio (Multi-threaded)

```rust
#[tokio::main]
async fn main() {
    let result = my_async_fn().await;
}

// Single-threaded runtime
#[tokio::main(flavor = "current_thread")]
async fn main() { ... }
```

### Runtime Builder (Manual)

```rust
let rt = tokio::runtime::Builder::new_multi_thread()
    .worker_threads(4)
    .enable_all()
    .build()
    .unwrap();

rt.block_on(async { ... });
```

### Common tokio features

```toml
tokio = { version = "1", features = ["full"] }
# Or selective:
tokio = { version = "1", features = ["rt-multi-thread", "macros", "fs", "net", "time", "sync"] }
```

## Concurrent Operations

### Join Multiple Futures

```rust
let (a, b, c) = tokio::join!(fetch_a(), fetch_b(), fetch_c());
// All run concurrently; waits for ALL to complete
```

### Select Between Futures

```rust
tokio::select! {
    result = future_a() => handle_a(result),
    result = future_b() => handle_b(result),
    // First to complete wins; others are DROPPED (cancelled)
}
```

**Cancellation Safety**: When `select!` drops a branch, the future is cancelled mid-execution. Not all futures are safe to cancel:
- Safe: `TcpStream::read`, channel `recv`
- Unsafe: operations that partially consumed a buffer

### Spawn (Background Task)

```rust
let handle = tokio::spawn(async move {
    expensive_computation().await
});
let result = handle.await.unwrap();  // JoinHandle<T>
```

### JoinSet (Multiple Spawned Tasks)

```rust
use tokio::task::JoinSet;

let mut set = JoinSet::new();
for url in urls {
    set.spawn(async move { fetch(url).await });
}

while let Some(result) = set.join_next().await {
    let response = result.unwrap();
    process(response);
}
```

## Timeouts

```rust
use tokio::time::{timeout, Duration};

match timeout(Duration::from_secs(5), slow_operation()).await {
    Ok(result) => println!("completed: {result:?}"),
    Err(_) => println!("timed out!"),
}
```

## Channels (Async)

```rust
use tokio::sync::{mpsc, oneshot, broadcast, watch};

// MPSC: multiple producers, single consumer
let (tx, mut rx) = mpsc::channel(100);
tx.send("hello").await.unwrap();
let msg = rx.recv().await;

// Oneshot: single value, single use
let (tx, rx) = oneshot::channel();
tx.send(42).unwrap();
let val = rx.await.unwrap();

// Broadcast: multiple consumers, each gets all messages
let (tx, _) = broadcast::channel(16);
let mut rx1 = tx.subscribe();
let mut rx2 = tx.subscribe();
```

## Async Streams

```rust
use tokio_stream::StreamExt;

let mut stream = tokio_stream::iter(1..10);
while let Some(value) = stream.next().await {
    println!("{value}");
}

// Async stream with async operations
use async_stream::stream;
let s = stream! {
    for i in 0..3 {
        let val = async_operation(i).await;
        yield val;
    }
};
```

## Async Mutex vs std Mutex

```rust
// std::sync::Mutex — OK if lock is held briefly and not across .await
let data = Arc::new(std::sync::Mutex::new(vec![]));

// tokio::sync::Mutex — required if lock is held across .await points
let data = Arc::new(tokio::sync::Mutex::new(vec![]));
let mut guard = data.lock().await;
some_async_op().await;  // holding lock across await — requires async Mutex
guard.push(42);
```

**Rule**: Prefer `std::sync::Mutex` unless you need to hold the lock across an `.await`.

## Error Handling

```rust
async fn process() -> anyhow::Result<()> {
    let data = fetch_data().await.context("fetching data")?;
    let parsed = parse(data).await.context("parsing response")?;
    save(parsed).await.context("saving result")?;
    Ok(())
}
```

## Reference Map

- `references/async-basics.md` — async/await fundamentals, state machine model
- `references/futures-pinning.md` — Future trait, Pin/Unpin, executors, wakers
- `references/tokio-patterns.md` — tokio runtime, spawning, channels, timeouts
- `references/streams-cancellation.md` — async streams, cancellation safety, structured concurrency

## Key Commands

```bash
cargo add tokio --features macros,rt-multi-thread,time,sync,fs,net
cargo add tokio-stream
cargo add async-stream
cargo add futures     # FuturesExt, StreamExt utilities
```

## Key References

- [Asynchronous Programming in Rust](https://rust-lang.github.io/async-book/)
- [tokio Tutorial](https://tokio.rs/tokio/tutorial)
- [Pin and Suffering](https://fasterthanli.me/articles/pin-and-suffering)
- [Cancellation Safety in tokio::select!](https://docs.rs/tokio/latest/tokio/macro.select.html)
