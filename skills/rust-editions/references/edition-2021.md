# Edition 2021

## Key Features

### Or Patterns

```rust
match value {
    Some(1) | Some(2) | Some(3) => println!("1-3"),
    _ => println!("other"),
}
```

### Closure Capture

```rust
let mut x = 0;
let inc = || x += 1;
```

### Type Aliases

```rust
type Result<T> = std::result::Result<T, Error>;
```

### Return Position impl Trait

```rust
fn foo() -> impl Trait {
    // ...
}
```
