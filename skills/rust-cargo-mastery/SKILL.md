---
name: rust-cargo-mastery
description: Master Cargo, the Rust package manager. Use when managing dependencies, configuring builds, setting up workspaces, publishing crates, using features for conditional compilation, or optimizing build times.
---

# Rust Cargo Mastery

Comprehensive guide to Cargo based on The Cargo Book.

## When to Use This Skill

- Setting up new Rust projects
- Managing dependencies from crates.io
- Configuring build profiles and features
- Working with Cargo workspaces
- Publishing crates to crates.io
- Optimizing build times
- Understanding Cargo's resolution algorithm

## Core References

- [The Cargo Book](https://doc.rust-lang.org/cargo/index.html) - Full Cargo documentation
- [Cargo Manifest](https://doc.rust-lang.org/cargo/reference/manifest.html) - Cargo.toml reference

## Dependencies

### Specifying Dependencies

```toml
[dependencies]
serde = "1.0"           # Exact or semver range
regex = "1.5"            # Minor version compatible
anyhow = "1.0"          # Any 1.x version
rand = { version = "0.8", features = ["small_rng"] }

[dev-dependencies]
criterion = "0.5"
```

### Version Syntax

| Syntax | Meaning |
|--------|---------|
| "1.0" | >=1.0.0, <2.0.0 |
| "=1.0.0" | Exact version |
| "^1.0" | >=1.0.0, <2.0.0 |
| "~1.0" | >=1.0.0, <1.1.0 |

### External Dependencies

```toml
[dependencies]
log = "0.4"
env_logger = { path = "../env_logger" }
my_crate = { git = "https://github.com/user/repo" }
```

## Workspaces

### Basic Workspace

```toml
# Cargo.toml (workspace root)
[workspace]
members = ["crate_a", "crate_b"]
resolver = "2"

[workspace.package]
version = "1.0.0"
edition = "2021"

[workspace.dependencies]
serde = "1.0"
```

```toml
# crate_a/Cargo.toml
[package]
name = "crate_a"
version.workspace = true
edition.workspace = true

[dependencies]
serde = { workspace = true }
```

### Workspace Features

```toml
[workspace]
members = ["lib", "binary"]

[workspace.dependencies]
my_lib = { path = "lib", default-features = false }
```

## Features

### Defining Features

```toml
[features]
default = ["std"]
std = []
derive = ["serde/derive"]
experimental = []
```

### Feature Unification

Workspace dependencies use unified features:
```toml
[dependencies]
common = { path = "../common", features = ["derive"] }
# Uses common's "derive" feature automatically
```

### Conditional Compilation

```rust
#[cfg(feature = "experimental")]
fn experimental_api() {}

#[cfg(not(feature = "std"))]
fn no_std_compatible() {}
```

## Build Profiles

### Custom Profiles

```toml
[profile.dev]
opt-level = 0
debug = true
split-debuginfo = "unpacked"

[profile.release]
opt-level = 3
lto = "thin"
codegen-units = 1

[profile.bench]
inherits = "release"
```

### Profile Override

```toml
[profile.dev.package."*"]
opt-level = 2
```

## Publishing

### Publishing to crates.io

```bash
cargo login
cargo publish
```

### Package Metadata

```toml
[package]
name = "my_crate"
version = "1.0.0"
edition = "2021"
description = "A short description"
license = "MIT"
license-file = "LICENSE"
repository = "https://github.com/user/repo"
documentation = "https://docs.rs/my_crate/"
homepage = "https://mycrate.io"
keywords = ["tag1", "tag2"]
categories = ["category1", "category2"]

[package.metadata.docs.rs]
all-features = true
rustdoc-args = ["--cfg", "docsrs"]
```

## Reference Map

- `references/dependencies.md` - Dependency specification, versions
- `references/workspaces.md` - Workspace configuration
- `references/features.md` - Feature flags, conditional compilation
- `references/profiles.md` - Build profiles, optimization
- `references/publishing.md` - Publishing to crates.io

## Common Commands

```bash
cargo new project_name
cargo build
cargo run
cargo test
cargo check
cargo doc
cargo publish
cargo update
cargo tree
cargo clippy
```

## Key References

- [The Cargo Book](https://doc.rust-lang.org/cargo/index.html)
- [Cargo Reference](https://doc.rust-lang.org/cargo/reference/index.html)
