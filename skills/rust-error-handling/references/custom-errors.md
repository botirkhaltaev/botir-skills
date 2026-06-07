# Custom Error Types

## The Error Trait

```rust
pub trait Error: Display + Debug {
    fn source(&self) -> Option<&(dyn Error + 'static)> { None }
}
```

Requirements:
- `Display` — user-facing message
- `Debug` — developer-facing details
- `source()` — optional nested error chain

## Manual Implementation

```rust
use std::fmt;

#[derive(Debug)]
pub enum DataError {
    NotFound { key: String },
    InvalidFormat { line: usize, message: String },
    Io(std::io::Error),
}

impl fmt::Display for DataError {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        match self {
            Self::NotFound { key } => write!(f, "key not found: {key}"),
            Self::InvalidFormat { line, message } => {
                write!(f, "invalid format at line {line}: {message}")
            }
            Self::Io(e) => write!(f, "I/O error: {e}"),
        }
    }
}

impl std::error::Error for DataError {
    fn source(&self) -> Option<&(dyn std::error::Error + 'static)> {
        match self {
            Self::Io(e) => Some(e),
            _ => None,
        }
    }
}

impl From<std::io::Error> for DataError {
    fn from(e: std::io::Error) -> Self { Self::Io(e) }
}
```

## Using thiserror (Libraries)

Eliminates boilerplate for the above:

```rust
use thiserror::Error;

#[derive(Debug, Error)]
pub enum DataError {
    #[error("key not found: {key}")]
    NotFound { key: String },

    #[error("invalid format at line {line}: {message}")]
    InvalidFormat { line: usize, message: String },

    #[error(transparent)]  // delegates Display and source
    Io(#[from] std::io::Error),
}
```

`#[from]` generates `From` impl. `#[error(...)]` generates `Display`. `#[source]` or `#[from]` sets up `source()`.

## Design Guidelines

1. **One enum per module/crate boundary** — don't have a single global error.
2. **Variants should be actionable** — callers should be able to match and recover.
3. **Keep variants domain-specific** — `ParseError::InvalidSyntax`, not `ParseError::StdIo`.
4. **Use `#[non_exhaustive]`** for public error enums to allow future variants:

```rust
#[derive(Debug, Error)]
#[non_exhaustive]
pub enum ApiError {
    #[error("rate limited")]
    RateLimited,
    #[error("unauthorized")]
    Unauthorized,
}
```

5. **Wrap generic errors** rather than exposing implementation details:

```rust
// Bad: leaks reqwest as public API
pub enum Error { Http(#[from] reqwest::Error) }

// Better: opaque wrapper
pub enum Error {
    #[error("network request failed")]
    Network { #[source] source: reqwest::Error },
}
```

## Error Context Pattern

Add context without changing the underlying error type:

```rust
use anyhow::Context;

let data = std::fs::read(path)
    .with_context(|| format!("failed to read {}", path.display()))?;
```

For library code without anyhow, use a wrapper variant:

```rust
#[derive(Debug, Error)]
pub enum LoadError {
    #[error("loading {path}: {source}")]
    File { path: PathBuf, source: std::io::Error },
}
```
