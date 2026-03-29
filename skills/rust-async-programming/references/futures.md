# Futures

## Future Trait

```rust
pub trait Future {
    type Output;
    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output>;
}
```

## Pinning

```rust
use std::pin::Pin;

let pinned: Pin<&mut dyn Future<Output = ()>> = Pin::new(&mut future);
```

## Boxed Futures

```rust
async fn boxed_example() -> Box<dyn Future<Output = i32> + Send> {
    Box::new(async { 42 })
}
```
