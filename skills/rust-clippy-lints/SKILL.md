---
name: rust-clippy-lints
description: Master Clippy lints for code quality. Use when running Clippy, fixing lint warnings, configuring lint levels, enabling/disabling specific lints, or writing custom lints.
---

# Clippy Lints

Guide to Clippy for code quality.

## When to Use This Skill

- Running Clippy checks
- Fixing lint warnings
- Configuring lint levels
- Enabling/disabling lints

## Running Clippy

```bash
cargo clippy
cargo clippy -- -D warnings
cargo clippy --all-targets
cargo clippy --all-features
```

## Common Lints

### Correctness

```rust
#[clippy::correctness]
// common issues
```

### Performance

```rust
#[clippy::performance]
// optimization suggestions
// - unnecessary_allocations
// - large_enum_variant
// - vec_resize_to_fill
```

### Style

```rust
#[clippy::style]
// idiomatic code suggestions
// - useless_format
// - default_trait_access
// - eq_op
```

### Pedantic

```rust
#[clippy::pedantic]
// strict suggestions
// - must_use_candidate
// - cast_precision_loss
```

## Allow/Forbid Lints

```rust
#[allow(clippy::lint_name)]
#[deny(clippy::lint_name)]
#[forbid(clippy::lint_name)]
```

## Common Fixes

### Unnecessary Clone

```rust
// Before
let x = some_value.clone();

// After (if ownership not needed)
let x = &some_value;
```

### Redundant Closure

```rust
// Before
.iter().map(|x| foo(x))

// After
.iter().map(foo)
```

## Reference Map

- `references/running-clippy.md` - Running commands
- `references/common-lints.md` - Important lints
- `references/configuring.md` - Configuration

## Lint Categories

- `clippy::correctness` - Bugs
- `clippy::complexity` - Overly complex
- `clippy::style` - Style
- `clippy::perf` - Performance
- `clippy::pedantic` - Strict
- `clippy::nursery` - Not yet stable

## Key References

- [Clippy Documentation](https://doc.rust-lang.org/clippy/)
- [Clippy Lints](https://rust-lang.github.io/rust-clippy/stable/lints/)
