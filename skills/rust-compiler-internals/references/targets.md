# Target Options

## Target CPU

```bash
-C target-cpu=native      # Use CPU on build machine
-C target-cpu=haswell    # Intel Haswell
-C target-cpu=skylake    # Intel Skylake
-C target-cpu=znver2     # AMD Zen 2
```

## Target Feature

```bash
-C target-feature=+sse4.1   # Enable
-C target-feature=-sse4.1   # Disable
```

## Common Targets

- `x86_64-unknown-linux-gnu`
- `x86_64-pc-windows-gnu`
- `aarch64-unknown-linux-gnu`
- `thumbv7em-none-eabihf` (embedded)
