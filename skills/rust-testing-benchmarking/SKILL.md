---
name: rust-testing-benchmarking
description: Master Rust testing and benchmarking. Use when writing unit tests, integration tests, documentation tests, property-based tests with proptest, statistical benchmarking with Criterion.rs, or setting up test infrastructure.
---

# Rust Testing & Benchmarking

Comprehensive guide to testing and benchmarking in Rust.

## When to Use This Skill

- Writing unit tests with #[test]
- Creating integration tests
- Writing documentation tests (doctests)
- Setting up property-based tests with proptest
- Benchmarking with Criterion.rs
- Testing async code

## Test Organization

### Unit Tests

```rust
#[cfg(test)]
mod tests {
    #[test]
    fn it_works() {
        assert_eq!(2 + 2, 4);
    }

    #[test]
    #[should_panic(expected = "divide by zero")]
    fn test_panic() {
        panic!("divide by zero");
    }

    #[test]
    fn result_test() -> Result<(), String> {
        if true {
            Ok(())
        } else {
            Err("failed".to_string())
        }
    }
}
```

### Integration Tests

```rust
// tests/integration_test.rs
use my_crate::add;

#[test]
fn test_integration() {
    assert_eq!(add(2, 2), 4);
}
```

### Doc Tests

```rust
/// Adds two numbers.
///
/// # Examples
///
/// ```
/// assert_eq!(add(2, 2), 4);
/// ```
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

Run with: `cargo test --doc`

## Property-Based Testing with proptest

### Setup

```toml
[dev-dependencies]
proptest = "1.0"
```

### Basic Usage

```rust
use proptest::prelude::*;

proptest! {
    #[test]
    fn test_addition(a in 0..1000i32, b in 0..1000i32) {
        assert!(a + b >= a);
        assert!(a + b >= b);
    }
}
```

### Custom Strategies

```rust
use proptest::strategy::Strategy;

fn non_empty_string() -> impl Strategy<Value = String> {
    "[a-z]{1,100}"
}

proptest! {
    #[test]
    fn test_string(s in non_empty_string()) {
        assert!(!s.is_empty());
    }
}
```

## Benchmarking with Criterion.rs

### Setup

```toml
[dev-dependencies]
criterion = "0.5"

[[bench]]
name = "my_benchmark"
harness = false
```

### Basic Benchmark

```rust
use criterion::{black_box, criterion_group, criterion_main, Criterion};

fn fibonacci(n: u64) -> u64 {
    match n {
        0 => 0,
        1 => 1,
        _ => fibonacci(n - 1) + fibonacci(n - 2),
    }
}

fn bench_fibonacci(c: &mut Criterion) {
    c.bench_function("fibonacci 20", |b| {
        b.iter(|| fibonacci(black_box(20)))
    });
}

criterion_group!(benches, bench_fibonacci);
criterion_main!(benches);
```

Run with: `cargo bench`

### Benchmark with Inputs

```rust
fn bench_vec_sort(c: &mut Criterion) {
    let mut group = c.benchmark_group("sort");
    
    for size in [100, 1000, 10000].iter() {
        let mut vec: Vec<i32> = (0..*size).collect();
        
        group.bench_with_input(format!("{}", size), &size, |b, &s| {
            b.iter(|| {
                let mut v = vec.clone();
                v.sort();
            })
        });
    }
    
    group.finish();
}
```

### Async Benchmarks

```rust
fn bench_async(c: &mut Criterion) {
    let rt = tokio::runtime::Runtime::new().unwrap();
    
    c.bench_function("async_task", |b| {
        b.to_async(&rt).iter(|| async {
            some_async_function().await
        })
    });
}
```

## Reference Map

- `references/unit-tests.md` - Unit test patterns
- `references/integration-tests.md` - Integration test setup
- `references/property-testing.md` - proptest strategies
- `references/benchmarking.md` - Criterion.rs setup

## Key Commands

```bash
cargo test
cargo test --test integration_test
cargo test --doc
cargo bench
cargo test --release
```

## Key References

- [Rust by Example - Testing](https://doc.rust-lang.org/stable/rust-by-example/testing.html)
- [Criterion.rs Book](https://bheisler.github.io/criterion.rs/book/)
- [proptest](https://docs.rs/proptest/)
