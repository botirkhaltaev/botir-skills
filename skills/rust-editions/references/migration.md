# Migration Guide

## Migrating to 2021

```bash
# Check migration
cargo fix --edition

# Apply migration
cargo fix --edition 2021
```

Update Cargo.toml:
```toml
[package]
edition = "2021"
```

## Common Issues

- Or patterns in match
- Closure captures
- Type alias reserve syntax
