# Workspaces

## Basic Workspace

```toml
[workspace]
members = ["crate_a", "crate_b"]
```

## Workspace Root Package

```toml
[workspace]
members = ["crate_a"]

[workspace.package]
version = "1.0.0"
edition = "2021"
authors = ["Author <email@examle.com>"]
```

Use in members:
```toml
[package]
name = "crate_a"
version.workspace = true
edition.workspace = true
```

## Workspace Dependencies

```toml
[workspace]
members = ["lib", "bin"]

[workspace.dependencies]
serde = "1.0"
utils = { path = "utils" }

[dependencies]
serde = { workspace = true }
```

## Workspace Resolver

```toml
[workspace]
resolver = "2"  # Recommended
```

## Exclude from Published Package

```toml
[package]
exclude = ["benches/", "tests/"]
```
