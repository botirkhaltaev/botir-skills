# Doc Syntax

## Doc Comments

```rust
/// Outer doc (for items)
pub struct Foo { }

mod inner {
    //! Inner doc (for modules)
}
```

## Sections

```rust
/// # Summary
/// # Description
/// # Parameters
/// # Returns
/// # Panics
/// # Errors
/// # Safety
/// # Examples
```

## Attributes

```rust
#[doc(alias = "alias")]
#[doc(hidden)]
#[doc(inline)]
#[doc(no_inline)]
```
