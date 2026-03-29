---
name: rust-editions
description: Master Rust editions, migration, and compatibility. Use when migrating between Rust editions (2015, 2018, 2021, 2024), understanding edition differences, using edition-specific features, or updating legacy code.
---

# Rust Editions

Guide to Rust editions and migration based on The Edition Guide.

## When to Use This Skill

- Migrating between Rust editions
- Understanding edition differences
- Using edition-specific features
- Updating legacy code

## Core References

- [The Edition Guide](https://doc.rust-lang.org/edition-guide/index.html)

## Edition Overview

| Edition | Year | Status |
|---------|------|--------|
| 2015 | 2015 | Stable |
| 2018 | 2018 | Stable |
| 2021 | 2021 | Stable |
| 2024 | 2024 | Coming |

## Edition 2015

Original edition. Key features:
- Basic Rust functionality
- Ownership system
- Borrowing and lifetimes
- Traits and generics

## Edition 2018

Major improvements:
- `?` operator for Option and Result
- Module system improvements
- Raw identifiers (`r#match`)
- Improved iterator chains
- `dyn Trait` for trait objects

### Key 2018 Changes

```rust
// ? operator for Option
fn foo(x: Option<i32>) -> Option<i32> {
    Some(x? + 1)
}

// Raw identifiers
let r#match = 42;

// dyn Trait
let x: Box<dyn Trait> = Box::new(Concrete::new());
```

## Edition 2021

Current stable edition. Key features:

### Or Patterns

```rust
match value {
    Some(1) | Some(2) | Some(3) => println!("one two or three"),
    _ => println!("other"),
}
```

### Closure Capture Improvements

```rust
let mut x = 0;
let increment = || x += 1;
```

### Type Aliases

```rust
type Result<T> = std::result::Result<T, Error>;
```

### Other Improvements

- Improved disjoint capture
- Return position impl Trait (RPIT)
- Array patterns

## Migration

### Using cargo fix

```bash
# Check what needs to change
cargo fix --edition

# Perform migration
cargo fix --edition 2021

# Update Cargo.toml
[package]
edition = "2021"
```

### Common Fixes

- Or patterns in match
- Closure captures
- Reserve syntax for type aliases

## Reference Map

- `references/edition-2015.md` - 2015 edition
- `references/edition-2018.md` - 2018 edition
- `references/edition-2021.md` - 2021 edition
- `references/migration.md` - Migration guide

## Key Commands

```bash
cargo fix --edition
cargo fix --edition 2021
```

## Key References

- [The Edition Guide](https://doc.rust-lang.org/edition-guide/index.html)
