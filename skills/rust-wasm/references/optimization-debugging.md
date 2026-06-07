# WASM Optimization & Debugging

## Size Optimization

### Cargo.toml Profile

```toml
[profile.release]
opt-level = "z"       # optimize for size (alternative: "s")
lto = true            # link-time optimization
codegen-units = 1     # single codegen unit for better optimization
strip = true          # strip debug symbols
panic = "abort"       # smaller than unwinding
```

### wasm-opt (binaryen)

```bash
# Install binaryen
apt-get install binaryen
# or: npm install -g binaryen

# Optimize the wasm file
wasm-opt -Oz -o optimized.wasm input.wasm
```

### Size Analysis with twiggy

```bash
cargo install twiggy

# Top largest functions
twiggy top pkg/my_lib_bg.wasm

# Dominator tree (what keeps what alive)
twiggy dominators pkg/my_lib_bg.wasm

# Diff between two builds
twiggy diff old.wasm new.wasm
```

### Size Reduction Techniques

1. **Avoid `format!`** in hot paths — string formatting pulls in large machinery
2. **Use `#[wasm_bindgen(skip)]`** on fields/methods you don't need exposed
3. **Feature-gate dependencies** — only enable what you use
4. **Replace `HashMap`** with `BTreeMap` or a simpler structure
5. **Use `wee_alloc`** as global allocator (smaller than default):

```rust
#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;
```

6. **Avoid panics** — each panic site adds string data
7. **Use `console_error_panic_hook`** only in debug builds

## Debugging

### console_error_panic_hook

Better panic messages in the browser console:

```rust
use std::panic;

#[wasm_bindgen(start)]
pub fn init() {
    panic::set_hook(Box::new(console_error_panic_hook::hook));
}
```

### Source Maps

```bash
# Build with debug info for source maps
wasm-pack build --dev
```

Chrome DevTools → Sources → can step through Rust source with DWARF info.

### Logging

```rust
// Simple: web_sys console
web_sys::console::log_1(&"message".into());
web_sys::console::log_2(&"key:".into(), &value.into());

// Structured: use `tracing` with `tracing-wasm`
use tracing_wasm::set_as_global_default;
set_as_global_default();
tracing::info!("initialized with config: {:?}", config);
```

## Testing

### wasm-pack test

```bash
wasm-pack test --headless --chrome
wasm-pack test --headless --firefox
wasm-pack test --node
```

```rust
#[cfg(test)]
mod tests {
    use super::*;
    use wasm_bindgen_test::*;

    wasm_bindgen_test_configure!(run_in_browser);

    #[wasm_bindgen_test]
    fn test_greet() {
        assert_eq!(greet("World"), "Hello, World!");
    }

    #[wasm_bindgen_test]
    async fn test_fetch() {
        let result = fetch_data("https://api.example.com").await;
        assert!(result.is_ok());
    }
}
```

## Performance Profiling

### Time API

```rust
use web_sys::window;

let performance = window().unwrap().performance().unwrap();
let start = performance.now();
// ... expensive work ...
let elapsed = performance.now() - start;
web_sys::console::log_1(&format!("took {elapsed:.2}ms").into());
```

### Chrome DevTools

1. Open Performance tab
2. Record while running WASM
3. Look for "wasm-function[N]" in flame chart
4. With source maps, see Rust function names

### Benchmarking Tips

- Compare against equivalent JS implementation
- Measure across the JS↔WASM boundary (copying data is expensive)
- For hot loops, pass pointers and operate on WASM memory directly
- Use `SharedArrayBuffer` + `Atomics` for multi-threaded WASM
