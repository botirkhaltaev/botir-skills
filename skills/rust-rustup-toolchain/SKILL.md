---
name: rust-rustup-toolchain
description: Master rustup for Rust toolchain management. Use when installing Rust, switching between stable/beta/nightly, managing multiple Rust versions, cross-compiling, or configuring toolchain overrides.
---

# Rustup Toolchain Management

Guide to rustup based on The rustup Book.

## When to Use This Skill

- Installing and managing Rust
- Switching between stable/beta/nightly
- Cross-compiling to different targets
- Using toolchain overrides
- Custom toolchains

## Core References

- [The rustup Book](https://rust-lang.github.io/rustup/index.html)

## Installation

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

## Toolchain Management

### Install Toolchain

```bash
rustup install stable
rustup install beta
rustup install nightly
rustup install 1.70.0
```

### Switch Default

```bash
rustup default stable
rustup default nightly
rustup default 1.70.0
```

### Update

```bash
rustup update
rustup update stable
rustup update nightly
```

## Targets

### Add Target

```bash
rustup target add thumbv7em-none-eabihf
rustup target add x86_64-unknown-linux-gnu
```

### List Targets

```bash
rustup target list --installed
```

## Cross-Compilation

### Example: ARM

```bash
rustup target add thumbv7em-none-eabihf
cargo build --target thumbv7em-none-eabihf
```

## Overrides

### Directory Override

```bash
rustup override set nightly
rustup override set 1.70.0
rustup override set stable
rustup override unset
```

### View Overrides

```bash
rustup show
```

## Reference Map

- `references/installation.md` - Installing rustup
- `references/toolchains.md` - Managing toolchains
- `references/cross-compilation.md` - Cross-compiling
- `references/overrides.md` - Override configuration

## Key Commands

```bash
rustup show
rustup toolchain list
rustup default
rustup override
rustup target
rustup update
```

## Key References

- [The rustup Book](https://rust-lang.github.io/rustup/index.html)
