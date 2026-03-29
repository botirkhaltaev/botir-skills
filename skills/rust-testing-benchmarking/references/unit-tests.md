# Unit Tests

## Basic Test

```rust
#[cfg(test)]
mod tests {
    #[test]
    fn test_example() {
        assert_eq!(2 + 2, 4);
    }
}
```

## Test Assertions

```rust
assert!(condition)
assert!(condition, "message")
assert_eq!(a, b)
assert_ne!(a, b)
assert_matches!(result, Ok(_))
```

## Setup/Teardown

```rust
#[cfg(test)]
mod tests {
    use super::*;

    fn setup() -> TestData {
        TestData::new()
    }

    #[test]
    fn test_something() {
        let data = setup();
        // test
    }
}
```

## Ignoring Tests

```rust
#[test]
#[ignore]
fn slow_test() {
    // ...
}
```
