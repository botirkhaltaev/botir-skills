# Doc Tests

## Running Tests

```bash
cargo test --doc
```

## Test Syntax

```rust
/// ```
/// let x = 2;
/// assert_eq!(x, 2);
/// ```
fn test_fn() {}
```

## Hidden Code

```rust
/// ```
/// # let x = setup();
/// # assert_eq!(x, 42);
/// ```
fn hidden_setup() {}
```

## Ignore Tests

```rust
/// ```
/// doctest: ignore
/// ```
fn ignore_this() {}

/// ```
/// doctest: +PASS
/// ```
fn expect_to_pass() {}
```
