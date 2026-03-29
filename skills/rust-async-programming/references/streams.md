# Async Streams

## Using tokio-stream

```rust
use tokio_stream::{StreamExt, iter};

let mut stream = iter(vec![1, 2, 3]);

while let Some(value) = stream.next().await {
    println!("{}", value);
}
```

## Creating Streams

```rust
fn my_stream() -> impl Stream<Item = i32> {
    tokio_stream::iter(1..10)
}
```

## Stream Transformations

```rust
stream
    .map(|x| x * 2)
    .filter(|x| x > 5)
    .collect::<Vec<_>>()
    .await;
```
