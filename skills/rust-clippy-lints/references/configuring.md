# Configuring Lints

## Allow Lint

```rust
#[allow(clippy::lint_name)]
fn foo() {}
```

## Warn Lint

```rust
#[warn(clippy::lint_name)]
fn foo() {}
```

## Deny Lint

```rust
#[deny(clippy::lint_name)]
fn foo() {}
```

## Forbid Lint

```rust
#![forbid(clippy::lint_name)]
```

## In Cargo.toml

```toml
[lints.clippy]
correctness = "deny"
style = "warn"
```
