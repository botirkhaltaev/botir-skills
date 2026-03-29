# Error Handling

## thiserror for Libraries

```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum Error {
    #[error("io error: {0}")]
    Io(#[from] std::io::Error),
    
    #[error("parse error: {0}")]
    Parse(String),
}
```

## anyhow for Applications

```rust
use anyhow::{Context, Result};

fn main() -> Result<()> {
    let data = load_file().context("load failed")?;
    Ok(())
}
```

## Custom Error Types

```rust
use std::fmt;

#[derive(Debug)]
pub struct MyError {
    message: String,
    code: i32,
}

impl fmt::Display for MyError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "{} (code: {})", self.message, self.code)
    }
}

impl std::error::Error for MyError {}
```
