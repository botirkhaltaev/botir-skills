# Property-Based Testing

## Setup

```toml
[dev-dependencies]
proptest = "1.0"
```

## Basic Usage

```rust
use proptest::prelude::*;

proptest! {
    #[test]
    fn test_addition(a in 0i32..1000, b in 0i32..1000) {
        assert!(a + b >= a);
    }
}
```

## Built-in Strategies

```rust
// Integers
any::<i32>()
0..1000i32
i32::MIN..i32::MAX

// Strings
".*"              // Any string
"[a-z]{1,10}"    // Regex pattern

// Collections
vec(any::<i32>(), 1..100)
```

## Custom Strategies

```rust
use proptest::strategy::Strategy;

fn positive_even() -> impl Strategy<Value = i32> {
    (1i32..).prop_map(|n| n * 2)
}
```
