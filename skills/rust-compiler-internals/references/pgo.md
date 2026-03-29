# Profile-Guided Optimization

## Generate Profile

```bash
# 1. Build with instrumentation
RUSTFLAGS="-C profile-generate" cargo build

# 2. Run workload
./target/debug/my_program

# 3. Find profile data
ls *.profraw
```

## Use Profile

```bash
# Merge profiles
llvm-profdata merge -o merged.profdata *.profraw

# Build with profile
RUSTFLAGS="-C profile-use=merged.profdata" cargo build --release
```
