# Running Clippy

## Basic Commands

```bash
cargo clippy
cargo clippy -- -D warnings
cargo clippy --all-targets
cargo clippy --all-features
cargo clippy --fix
```

## CI Usage

```bash
cargo clippy -- -D warnings -D clippy::all
```
