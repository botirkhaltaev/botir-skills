# Intra-Doc Links

## Basic Syntax

```rust
/// See [`OtherType`] for details.
struct MyType;

/// Use [`MyType::method`].
fn use_it() {}
```

## Link Types

```rust
/// [`StructName`]
/// [`TraitName`]
/// [`fn function_name`]
/// [`method`](StructName::method)
/// [`Self`]
/// [`crate::path::Item`]
```

## Module Links

```rust
/// Re-exports: [`module::Item`](crate::module::Item)
```
