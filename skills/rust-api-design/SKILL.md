---
name: rust-api-design
description: Design idiomatic Rust APIs following best practices. Use when designing public crate APIs, creating libraries, writing documentation, choosing naming conventions, implementing builder patterns, or following the Rust API Guidelines.
---

# Rust API Design

Guide to designing idiomatic Rust APIs based on the Rust API Guidelines.

## When to Use This Skill

- Designing public crate APIs
- Creating library code
- Writing documentation for libraries
- Choosing naming conventions
- Implementing builder patterns
- Ensuring API consistency

## Core References

- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/) - Official API guidelines
- [Rust API Guidelines Checklist](https://rust-lang.github.io/api-guidelines/checklist.html) - Checklist

## Naming Conventions

### Functions and Methods

```rust
// Getters
struct Person { name: String }
impl Person {
    fn name(&self) -> &str { &self.name }
}

// Boolean methods
impl Iterator {
    fn next(&mut self) -> Option<Self::Item>;
    fn len(&self) -> usize;
    fn is_empty(&self) -> bool { self.len() == 0 }
}
```

### Types and Traits

```rust
// Types: PascalCase
struct ConfigFile;
enum HttpError;
type Result<T> = std::result::Result<T, Error>;

// Traits: adjectives (capable of X) or nouns (handler for X)
trait Readable;
trait Iterator;
trait Formatter;
```

### Modules

```rust
mod io;      // Collection of I/O utilities
mod sync;    // Synchronization primitives
pub mod error; // Re-exports error types
```

## Documentation

### Doc Comments

```rust
/// Short description (one line).
/// 
/// Longer description with multiple lines.
/// 
/// # Panics
/// 
/// This function panics if x is 0.
/// 
/// # Examples
/// 
/// ```
/// assert_eq!(foo(2), 4);
/// ```
pub fn foo(x: i32) -> i32 {
    x * 2
}
```

### Module Documentation

```rust
//! # My Crate
//! 
//! A brief description of what this crate does.
//!
//! ## Usage
//!
//! Add to your Cargo.toml:
//!
//! ```toml
//! [dependencies]
//! my_crate = "1.0"
//! ```
```

## Error Handling

### Use thiserror for Libraries

```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum MyError {
    #[error("configuration error: {0}")]
    Config(#[from] std::io::Error),
    
    #[error("parse error at position {position}: {message}")]
    Parse { position: usize, message: String },
}
```

### Use anyhow for Applications

```rust
use anyhow::{Context, Result};

fn main() -> Result<()> {
    let config = load_config()
        .context("Failed to load configuration")?;
    // ...
}
```

## Builder Pattern

```rust
pub struct Job {
    name: String,
    priority: i32,
    retries: u32,
}

impl Job {
    pub fn new(name: &str) -> Self {
        Self {
            name: name.to_string(),
            priority: 0,
            retries: 0,
        }
    }
    
    pub fn priority(mut self, priority: i32) -> Self {
        self.priority = priority;
        self
    }
    
    pub fn retries(mut self, retries: u32) -> Self {
        self.retries = retries;
        self
    }
}

// Usage
let job = Job::new("build")
    .priority(10)
    .retries(3);
```

## Reference Map

- `references/naming.md` - Naming conventions
- `references/documentation.md` - Writing docs
- `references/error-handling.md` - Error patterns
- `references/ builders.md` - Builder pattern
- `references/interoperability.md` - C/FFI compatibility

## Key Principles

1. **Cargo feature organization** - Put new features behind flags
2. **Semantic versioning** - Follow semver for breaking changes
3. **Minimal surface area** - Expose only what's necessary
4. **Make invalid states unrepresentable** - Use types

## Key References

- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)
- [thiserror](https://docs.rs/thiserror/)
- [anyhow](https://docs.rs/anyhow/)
