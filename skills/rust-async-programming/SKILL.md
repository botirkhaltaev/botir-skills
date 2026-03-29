---
name: rust-async-programming
description: Master async/await programming in Rust. Use when writing async functions, working with Futures, using async runtimes like tokio, handling streams, implementing select/join patterns, or debugging async code.
---

# Async Programming in Rust

Comprehensive guide to async/await based on Asynchronous Programming in Rust.

## When to Use This Skill

- Writing async/await code
- Understanding Futures and executors
- Using tokio or async-std runtimes
- Working with async streams
- Implementing concurrent operations with select/join
- Handling async errors

## Core References

- [Asynchronous Programming in Rust](https://rust-lang.github.io/async-book/)
- [tokio](https://tokio.rs) - Popular async runtime

## async/await Basics

### Async Functions

```rust
async fn fetch_data() -> Result<String, Error> {
    let response = reqwest::get("https://example.com").await?;
    let body = response.text().await?;
    Ok(body)
}
```

### The .await Operator

```rust
async fn example() {
    let result = future.await;
    // Continues when future completes
}
```

## Futures

### Understanding Future

```rust
trait Future {
    type Output;
    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output>;
}
```

### Pinning

```rust
use std::pin::Pin;
use std::future::Future;

fn pinned_example(f: Pin<&mut dyn Future<Output = i32>>) {}
```

## Async Runtimes

### Using tokio

```rust
#[tokio::main]
async fn main() {
    let result = my_async_fn().await;
}

async fn my_async_fn() -> String {
    "hello".to_string()
}
```

### Common tokio features

```toml
tokio = { version = "1", features = ["full"] }
tokio = { version = "1", features = ["rt-multi-thread", "macros", "fs", "net"] }
```

## Concurrent Operations

### Join Multiple Futures

```rust
async fn fetch_all() {
    let (a, b, c) = tokio::join!(fetch_a(), fetch_b(), fetch_c());
}
```

### Select Between Futures

```rust
async fn race() {
    tokio::select! {
        result = future_a() => handle_a(result),
        result = future_b() => handle_b(result),
    }
}
```

### Race (first to complete)

```rust
async fn race() {
    let result = tokio::race!(timeout_a(), timeout_b()).await;
}
```

## Async Streams

### Creating Streams

```rust
use tokio_stream::StreamExt;

async fn stream_example() {
    let mut stream = tokio_stream::iter(1..10);
    
    while let Some(value) = stream.next().await {
        println!("{}", value);
    }
}
```

## Error Handling

```rust
async fn handle_error() -> Result<String, Error> {
    let result = maybe_fail().await?;
    Ok(result)
}

async fn maybe_fail() -> Result<String, Error> {
    Err(Error::new("failed"))
}
```

## Reference Map

- `references/async-basics.md` - async/await fundamentals
- `references/futures.md` - Future trait and Pin
- `references/tokio.md` - tokio runtime usage
- `references/streams.md` - Async streams

## Key Commands

```bash
cargo add tokio --features macros,rt-multi-thread
cargo add async-std
cargo add tokio-stream
```

## Key References

- [Asynchronous Programming in Rust](https://rust-lang.github.io/async-book/)
- [tokio](https://tokio.rs)
- [async-std](https://async.rs)
