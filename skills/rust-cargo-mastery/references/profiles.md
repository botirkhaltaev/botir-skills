# Build Profiles

## Default Profiles

- `dev`: Development builds
- `release`: Release builds
- `test`: Test builds
- `bench`: Benchmark builds

## Custom Profile Settings

```toml
[profile.release]
opt-level = 3        # 0-3
lto = "thin"         # "thin", "fat", true, false
codegen-units = 1    # 1-16
strip = true         # Strip symbols
panic = "unwind"     # "unwind", "abort"
```

## Profile Overrides

```toml
[profile.dev.package."*"]
opt-level = 2
```

## Debug Information

```toml
[profile.dev]
debug = true         # Full debug info
split-debuginfo = "unpacked"  # Faster builds on macOS
```
