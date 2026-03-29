# Integration Tests

## Location

Integration tests go in `tests/` directory:
```
src/lib.rs
tests/
  integration_test.rs
  another_test.rs
```

## Running Integration Tests

```bash
cargo test --test integration_test
cargo test --tests
```

## Using Library Code

```rust
// tests/my_test.rs
use my_library::add;

#[test]
fn test_add() {
    assert_eq!(add(2, 2), 4);
}
```

## Test Binaries

```
tests/
  bin/
    test_binary.rs
```
