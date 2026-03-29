# Naming Conventions

## Functions

```rust
// Use snake_case
fn calculate_total() {}
fn process_data() {}

// Getters
fn name(&self) -> &str {}

// Boolean: is_, has_, can_, etc.
fn is_empty(&self) -> bool {}
fn has_value(&self) -> bool {}
```

## Types

```rust
// PascalCase
struct MyStruct;
enum MyEnum;
type MyResult<T> = Result<T, Error>;
trait MyTrait;
```

## Constants

```rust
const MAX_BUFFER_SIZE: usize = 1024;
const DEFAULT_TIMEOUT: Duration = Duration::from_secs(30);
```
