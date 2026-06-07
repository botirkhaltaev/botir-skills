---
name: rust-error-handling
description: Idiomatic Rust error handling with Result, Option, the ? operator, custom error types, thiserror, and anyhow. Use when designing error hierarchies, composing fallible operations, converting between error types, or choosing between library and application error strategies.
---

# Rust Error Handling

Comprehensive guide based on The Rust Programming Language Ch. 9, Effective Rust Item 4, and Rust by Example.

## When to Use This Skill

- Choosing between `panic!`, `Result`, and `Option`
- Designing custom error types for a library or application
- Composing multiple error types with `?`
- Deciding between `thiserror` (libraries) and `anyhow` (applications)
- Converting errors with `From`/`Into`
- Wrapping context around errors

## Decision Tree

```
Is recovery possible?
├── No  → panic! / unwrap / expect (bugs, invariant violations)
└── Yes
    ├── Value might be absent (not an error) → Option<T>
    └── Operation can fail → Result<T, E>
        ├── Library crate → custom enum + thiserror
        └── Application → anyhow::Result or Box<dyn Error>
```

## The ? Operator

Propagates errors by early-returning `Err(e.into())`:

```rust
use std::fs;
use std::io;

fn read_config() -> Result<String, io::Error> {
    let path = fs::read_to_string("config.toml")?;  // returns Err on failure
    Ok(path)
}
```

`?` calls `From::from(err)` on the error, enabling automatic conversion.

## Custom Error Types (Library Pattern)

```rust
use thiserror::Error;

#[derive(Debug, Error)]
pub enum ParseError {
    #[error("invalid header at byte {offset}")]
    InvalidHeader { offset: usize },

    #[error("unsupported version {0}")]
    UnsupportedVersion(u32),

    #[error(transparent)]
    Io(#[from] std::io::Error),
}
```

Key rules:
- Implement `std::error::Error` (thiserror does this automatically)
- Implement `Display` for user-facing messages
- Implement `From<SubError>` for each wrapped error type
- Keep variants domain-specific and actionable

## Application Error Pattern (anyhow)

```rust
use anyhow::{Context, Result};

fn main() -> Result<()> {
    let config = std::fs::read_to_string("app.toml")
        .context("failed to read config file")?;
    let parsed = parse_config(&config)
        .context("config parsing failed")?;
    run(parsed)?;
    Ok(())
}
```

`anyhow` provides:
- `anyhow::Error` — type-erased, carries a chain of context
- `.context("msg")` — wraps error with human-readable description
- `.with_context(|| format!(...))` — lazy context formatting
- Downcasting: `err.downcast_ref::<MyError>()`

## Option Combinators

```rust
let port: Option<u16> = env::var("PORT").ok()
    .and_then(|s| s.parse().ok());

let port = port.unwrap_or(8080);  // default fallback
```

| Method | Purpose |
|--------|---------|
| `map(f)` | Transform inner value |
| `and_then(f)` | Chain fallible transforms |
| `unwrap_or(v)` | Default value |
| `unwrap_or_else(f)` | Lazy default |
| `ok_or(e)` | Convert to Result |
| `transpose()` | `Option<Result>` ↔ `Result<Option>` |

## Result Combinators

| Method | Purpose |
|--------|---------|
| `map(f)` | Transform Ok value |
| `map_err(f)` | Transform Err value |
| `and_then(f)` | Chain fallible operations |
| `or_else(f)` | Try alternative on error |
| `unwrap_or_default()` | Use `Default` on error |
| `context("msg")` | (anyhow) Add context |

## When to panic!

- Invariant violations that indicate bugs (not user input errors)
- Setup/initialization that can't recover (missing required env vars at startup)
- Tests and examples
- `unreachable!()` for logically impossible branches
- Prototype code (prefer `todo!()` over `unwrap()`)

## Error Composition Pattern

```rust
#[derive(Debug, Error)]
pub enum AppError {
    #[error("database error")]
    Db(#[from] diesel::result::Error),

    #[error("network error")]
    Network(#[from] reqwest::Error),

    #[error("validation: {0}")]
    Validation(String),
}
```

## Reference Map

- `references/result-option.md` — Result/Option APIs, combinators, conversion
- `references/custom-errors.md` — designing error enums, thiserror patterns
- `references/anyhow-context.md` — application-level error handling, context chains

## Key References

- [The Rust Programming Language, Ch. 9](https://doc.rust-lang.org/book/ch09-00-error-handling.html)
- [Effective Rust, Item 4](https://www.lurklurk.org/effective-rust/errors.html)
- [thiserror](https://docs.rs/thiserror)
- [anyhow](https://docs.rs/anyhow)
