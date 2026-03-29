# Benchmarking

## Criterion.rs Setup

```toml
[dev-dependencies]
criterion = "0.5"

[[bench]]
name = "my_bench"
harness = false
```

## Basic Benchmark

```rust
use criterion::{black_box, criterion_group, criterion_main, Criterion};

fn bench_function(c: &mut Criterion) {
    c.bench_function("my_function", |b| {
        b.iter(|| my_function(black_box(42)))
    });
}

criterion_group!(benches, bench_function);
criterion_main!(benches);
```

## Parameterized Benchmarks

```rust
fn bench_sort(c: &mut Criterion) {
    let mut group = c.benchmark_group("sort");
    
    for size in [100, 1000, 10000] {
        let mut vec: Vec<i32> = (0..size).collect();
        group.bench_with_input(size, &size, |b, &s| {
            b.iter(|| {
                let mut v = vec.clone();
                v.sort();
            })
        });
    }
}
```
