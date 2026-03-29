# Async Basics

## async fn

```rust
async fn hello() -> String {
    "hello".to_string()
}
```

## .await

```rust
async fn main() {
    let result = hello().await;
}
```

## async Blocks

```rust
let future = async {
    let a = do_something().await;
    let b = do_something_else().await;
    a + b
};
```
