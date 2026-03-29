# Edition Migration

## Migrating to 2021

```bash
cargo fix --edition
cargo fix --edition 2021
```

Update Cargo.toml:
```toml
[package]
edition = "2021"
```

## Key Changes in 2021

- Or patterns in match
- Closure capture improvements
- Reserve syntax for type aliases
