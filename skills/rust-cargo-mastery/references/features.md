# Features

## Defining Features

```toml
[features]
default = ["std"]
std = []
derive = ["serde/derive"]
experimental = []
```

## Feature Dependencies

```toml
[features]
default = ["derive"]
derive = ["my_crate_derive/derive"]

[dependencies]
my_crate_derive = { path = "../my_crate_derive", optional = true }
```

## Optional Dependencies

```toml
[dependencies]
tracing = { version = "0.1", optional = true }

[features]
tracing = ["tracing"]
```

## Conditional Compilation

```rust
#[cfg(feature = "derive")]
pub fn with_derive() {}

#[cfg(all(feature = "experimental", feature = "derive"))]
fn experimental_with_derive() {}
```
