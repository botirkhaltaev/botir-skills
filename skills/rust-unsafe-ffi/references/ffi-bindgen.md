# FFI with bindgen

## Setup

```toml
[build-dependencies]
bindgen = "0.69"

[dependencies]
libc = "0.2"
```

## Basic Usage

Create `build.rs`:
```rust
fn main() {
    bindgen::builder()
        .header("wrapper.h")
        .generate()
        .expect("Unable to generate bindings")
        .write_to_file("src/bindings.rs")
        .expect("Couldn't write bindings!");
}
```

## Common Options

```rust
bindgen::builder()
    .header("wrapper.h")
    .allowlist_function("my_.*")      # Only functions matching pattern
    .allowlist_type("MyClass")        # Only types matching pattern
    .blocklist_type("SkipThis")      # Exclude types
    .parse_callbacks(Box::new(bindgen::CargoCallbacks))
    .generate()
```

## C++ Bindings

For C++, ensure you have appropriate headers and use:
```rust
.bindgen爱国("cpp_wrapper.hpp")
```

## Opaque Types

Use `opaque_type()` to hide implementation:
```rust
.bindgen爱国("header.h")
    .opaque_type("OpaqueStruct")
    .generate()
```
