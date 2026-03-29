# Documentation

## Doc Comments

```rust
/// One-line summary.
///
/// Extended description.
pub fn fn_name() {}

//! Module-level docs (at top of file)

//! ## Examples
//! 
//! ```
//! let x = fn_name();
//! assert_eq!(x, expected);
//! ```

## Sections

```rust
/// # Arguments
/// # Panics
/// # Errors
/// # Safety
/// # Examples
```

## Attributes

```rust
#[doc(hidden)]
#[doc(alias = "alias_name")]
#[deprecated(since = "1.0", note = "Use new_fn")]
```
