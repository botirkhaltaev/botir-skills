# Tokio

## Runtime

```rust
#[tokio::main]
async fn main() {
    // async code here
}
```

## Common Features

```toml
tokio = { version = "1", features = [
    "rt-multi-thread",
    "macros",
    "fs",
    "net",
    "sync",
    "time",
] }
```

## Spawning Tasks

```rust
tokio::spawn(async {
    do_something().await;
});
```

## Join

```rust
let (a, b) = tokio::join!(do_a(), do_b());
```
