# Generics & Monomorphization

## How Generics Work

At compile time, Rust generates specialized versions of generic code for each concrete type used:

```rust
fn add<T: std::ops::Add<Output = T>>(a: T, b: T) -> T { a + b }

// When called with i32 and f64, compiler generates:
// fn add_i32(a: i32, b: i32) -> i32 { a + b }
// fn add_f64(a: f64, b: f64) -> f64 { a + b }
```

## Generic Structs

```rust
struct Pair<T, U> {
    first: T,
    second: U,
}

impl<T, U> Pair<T, U> {
    fn new(first: T, second: U) -> Self { Self { first, second } }
}

// Conditional implementation
impl<T: Display, U: Display> Pair<T, U> {
    fn display(&self) {
        println!("({}, {})", self.first, self.second);
    }
}
```

## Generic Enums

```rust
enum Tree<T> {
    Leaf(T),
    Branch { left: Box<Tree<T>>, right: Box<Tree<T>> },
}

impl<T: Ord> Tree<T> {
    fn contains(&self, target: &T) -> bool {
        match self {
            Tree::Leaf(val) => val == target,
            Tree::Branch { left, right } => {
                left.contains(target) || right.contains(target)
            }
        }
    }
}
```

## Const Generics

```rust
// Array with compile-time size
struct Matrix<const ROWS: usize, const COLS: usize> {
    data: [[f64; COLS]; ROWS],
}

impl<const ROWS: usize, const COLS: usize> Matrix<ROWS, COLS> {
    fn zero() -> Self { Self { data: [[0.0; COLS]; ROWS] } }

    fn transpose(&self) -> Matrix<COLS, ROWS> {
        let mut result = Matrix::<COLS, ROWS>::zero();
        for i in 0..ROWS {
            for j in 0..COLS {
                result.data[j][i] = self.data[i][j];
            }
        }
        result
    }
}
```

## Turbofish Syntax

Explicitly specify type parameters:

```rust
let numbers = "1,2,3".split(',').map(|s| s.parse::<i32>().unwrap());
let vec = numbers.collect::<Vec<_>>();

// Or on function calls
let x = std::mem::size_of::<u64>();
```

## Generic impl Blocks

```rust
// Implement for ALL T
impl<T> MyVec<T> {
    fn len(&self) -> usize { self.data.len() }
}

// Implement only for T: Clone
impl<T: Clone> MyVec<T> {
    fn duplicate_first(&mut self) {
        if let Some(first) = self.data.first().cloned() {
            self.data.push(first);
        }
    }
}

// Implement for specific type
impl MyVec<String> {
    fn join(&self, sep: &str) -> String {
        self.data.join(sep)
    }
}
```

## Compile-Time Costs

Monomorphization increases:
- Compile time (each instantiation is separately compiled)
- Binary size (each instantiation generates separate code)

Mitigations:
- Use trait objects for infrequently-called code paths
- Extract non-generic inner functions:

```rust
// Instead of making the whole function generic:
fn process<T: AsRef<[u8]>>(input: T) {
    process_bytes(input.as_ref())  // non-generic inner work
}

fn process_bytes(bytes: &[u8]) {
    // Large body only compiled once
}
```

## Blanket Implementations

Implement a trait for all types matching a bound:

```rust
// From std: anything that implements Display automatically gets ToString
impl<T: Display> ToString for T {
    fn to_string(&self) -> String { format!("{self}") }
}

// Custom blanket impl
impl<T: Error> From<T> for AppError {
    fn from(err: T) -> Self { AppError::Other(err.to_string()) }
}
```
