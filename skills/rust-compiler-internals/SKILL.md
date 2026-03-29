---
name: rust-compiler-internals
description: Understand Rust compiler internals and rustc usage. Use when optimizing build times, understanding compiler errors, using codegen options, setting up PGO, or contributing to Rust.
---

# Rust Compiler Internals

Guide to rustc and compiler internals based on The rustc Book.

## When to Use This Skill

- Optimizing build times
- Understanding compiler errors
- Using codegen options
- Setting up profile-guided optimization (PGO)
- Contributing to Rust compiler

## Core References

- [The rustc Book](https://doc.rust-lang.org/rustc/index.html)
- [Guide to Rustc Development](https://rustc-dev-guide.rust-lang.org/)

## Compiler Options

### Codegen Units

```bash
# Single codegen unit (slower compile, more optimization)
RUSTFLAGS="-C codegen-units=1" cargo build

# Multiple codegen units (faster compile)
RUSTFLAGS="-C codegen-units=16" cargo build
```

### Optimization Levels

```bash
# No optimization
RUSTFLAGS="-C opt-level=0" cargo build

# Release optimization
RUSTFLAGS="-C opt-level=3" cargo build
```

## Link Time Optimization (LTO)

```bash
# Thin LTO
RUSTFLAGS="-C lto=thin" cargo build

# Fat LTO
RUSTFLAGS="-C lto=fat" cargo build
```

## Profile-Guided Optimization (PGO)

### Generate Profile

```bash
# Build with instrumentation
RUSTFLAGS="-C profile-generate" cargo build
# Run your benchmarks/tests
# Generate training data

# Create profile data
rustup profile new --directory pgo_data
```

### Use Profile

```bash
RUSTFLAGS="-C profile-use=profdata" cargo build
```

## Target Options

### CPU-Specific

```bash
# Native CPU
RUSTFLAGS="-C target-cpu=native" cargo build

# Specific CPU
RUSTFLAGS="-C target-cpu=haswell" cargo build
```

## Reference Map

- `references/codegen.md` - Codegen options
- `references/lto.md` - Link time optimization
- `references/pgo.md` - Profile-guided optimization
- `references/targets.md` - Target options

## Build Scripts

### build.rs

```rust
fn main() {
    println!("cargo:rerun-if-changed=build.rs");
    println!("cargo:rerun-if-env-changed=MY_VAR");
}
```

## Key Flags

```bash
-C opt-level=N        # Optimization level
-C lto=thin|fat|true # LTO
-C codegen-units=N   # Codegen units
-C target-cpu=native # CPU target
-g                   # Include debug info
```

## Key References

- [The rustc Book](https://doc.rust-lang.org/rustc/index.html)
- [Guide to Rustc Development](https://rustc-dev-guide.rust-lang.org/)
