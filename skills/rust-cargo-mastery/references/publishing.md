# Publishing

## Publish to crates.io

```bash
cargo login
cargo publish
```

## Dry Run

```bash
cargo publish --dry-run
```

## Package Metadata

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
categories = ["category1"]
```

## Exclude Files

```toml
[package]
exclude = ["benches/**", "tests/**", "*.md"]
```

## Workspace Publishing

```toml
[package]
publish = false  # Don't publish root
```
