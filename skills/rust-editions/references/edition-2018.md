# Edition 2018

## Key Features

### ? Operator for Option

```rust
fn foo(x: Option<i32>) -> Option<i32> {
    Some(x? + 1)
}
```

### Raw Identifiers

```rust
let r#match = 42;
fn r#type() {}
```

### Improved Modules

```rust
mod outer {
    mod inner {
        // Use `super` and `crate`
    }
}
```

### dyn Trait

```rust
let x: Box<dyn Trait> = Box::new(Concrete::new());
```
