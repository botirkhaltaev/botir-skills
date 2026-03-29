---
name: rust-rustdoc
description: Master rustdoc for generating documentation. Use when documenting Rust code, writing doc tests, configuring doc builds, publishing to docs.rs, using intra-doc links, or customizing documentation output.
---

# Rust Documentation with rustdoc

Comprehensive guide to rustdoc based on The rustdoc Book.

## When to Use This Skill

- Generating documentation with cargo doc
- Writing doc comments and doctests
- Publishing documentation to docs.rs
- Using intra-doc links
- Configuring documentation builds

## Core References

- [The rustdoc Book](https://doc.rust-lang.org/stable/rustdoc/)

## Basic Documentation

### Documenting Functions

```rust
/// Adds two numbers together.
///
/// # Examples
///
/// ```
/// assert_eq!(add(2, 2), 4);
/// ```
///
/// # Panics
///
/// Does not panic.
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

### Documenting Structs

```rust
/// A person with a name and age.
pub struct Person {
    /// The person's name.
    pub name: String,
    /// The person's age.
    pub age: u32,
}
```

### Module Documentation

```rust
//! # My Module
//!
//! This module provides useful utilities.

pub mod submodule {
    //! Submodule documentation.
}
```

## Doc Tests

### Running Doc Tests

```bash
cargo test --doc
```

### Hide Code from Docs

```rust
/// ```
/// # This won't show in docs
/// let x = 42;
/// ```
fn hidden_example() {}
```

### Ignoring Tests

```rust
/// ```
/// # doctest: ignore
/// let x = complex_setup();
/// ```
fn ignore_test() {}

/// ```
/// doctest: +PASS
/// ```
fn expect_pass() {}
```

## Intra-Doc Links

### Basic Links

```rust
/// See [`OtherStruct`] for more details.
pub struct MyStruct;

/// Use [`Self`] to refer to current type.
impl MyStruct {
    /// Method returning [`Option::None`].
    fn foo() {}
}
```

### Link to Methods

```rust
/// Call [`Vec::push`] to add elements.
fn use_vec() {}
```

### Link with Custom Text

```rust
/// Use the [`add`](fn.add.html) function.
```

## Command Line Options

```bash
cargo doc                 # Build docs
cargo doc --open         # Build and open
cargo doc --no-deps      # Don't build dependency docs
cargo doc --document-private-items  # Include private items
```

## Reference Map

- `references/doc-syntax.md` - Doc comment syntax
- `references/doc-tests.md` - Doctest patterns
- `references/intra-doc-links.md` - Linking between items

## Publishing to docs.rs

Automatic when you publish to crates.io:

```bash
cargo publish
```

Configuration in `docsrs`:
```toml
[package.metadata.docs.rs]
all-features = true
rustdoc-args = ["--cfg", "docsrs"]
```

## Key References

- [The rustdoc Book](https://doc.rust-lang.org/stable/rustdoc/)
- [docs.rs](https://docs.rs/)
