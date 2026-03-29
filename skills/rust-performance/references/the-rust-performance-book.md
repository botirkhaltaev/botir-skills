# The Rust Performance Book

Based on: https://nnethercote.github.io/perf-book/

## Core Principles

1. **Measure first** - Always establish a baseline
2. **Change one thing at a time** - For clear causality
3. **Prefer release builds** - For runtime conclusions
4. **Consider trade-offs** - Speed vs memory vs compile time etc.

## Key Areas

### Benchmarking

- Use criterion.rs for statistical rigor
- Consider Hyperfine for simple comparisons
- Use real-world workloads when possible
- Microbenchmarks have limitations but can be useful

### Build Configuration

Optimize profiles in Cargo.toml:
- `opt-level = 3` for maximum optimization
- `lto = "thin"` or `"fat"` for link-time optimization  
- `codegen-units = 1` for better optimization (slower builds)
- `incremental = false` for release builds
- `debuginfo = 0` to strip debug info
- `panic = "abort"` for smaller binaries

### Memory Optimization

- Reuse buffers and allocations
- Prefer stack allocation over heap
- Use object pools for frequent allocations
- Minimize unnecessary copying
- Consider custom allocators for specific needs

### Binary Size

- Enable LTO (Link Time Optimization)
- Use `panic = "abort"`
- Strip symbols post-build
- Remove unused dependencies
- Consider `#![no_std]` for embedded

### Compile Times

- Reduce monomorphization where possible
- Limit generic code in hot paths
- Use incremental compilation during development
- Optimize build scripts and proc macros
- Split large crates when appropriate

### CPU-Specific

- Target specific CPU: `RUSTFLAGS="-C target-cpu=native"`
- Use SIMD when appropriate
- Consider profile-guided optimization (PGO)
- Tune allocator for your workload
- Monitor instruction counts and cycles

## Reference Map

- `references/measurement.md` - Benchmarking and profiling
- `references/build-configuration.md` - Cargo profile optimization
- `references/allocations-layout.md` - Memory and allocation strategies
- `references/compile-times.md` - Build time optimization