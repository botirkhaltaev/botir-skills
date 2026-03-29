# Cross-Compilation

## Add Target

```bash
rustup target add thumbv7em-none-eabihf
```

## Build for Target

```bash
cargo build --target thumbv7em-none-eabihf
```

## Common Targets

- `thumbv7em-none-eabihf` - ARM Cortex-M4F
- `thumbv7em-none-eabi` - ARM Cortex-M4
- `x86_64-unknown-linux-gnu` - Linux x86_64
- `x86_64-pc-windows-gnu` - Windows
