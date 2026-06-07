---
name: rust-wasm
description: Compile Rust to WebAssembly for browser and Node.js environments. Use when building wasm modules with wasm-bindgen, creating JS interop, using web-sys/js-sys, optimizing wasm size, or deploying Rust+WASM to production.
---

# Rust & WebAssembly

Based on the Rust and WebAssembly book, the wasm-bindgen guide, and wasm-pack documentation.

## When to Use This Skill

- Compiling Rust to `.wasm` for the browser
- Interoperating with JavaScript (functions, objects, strings)
- Using DOM APIs from Rust via `web-sys`
- Publishing Rust-WASM packages to npm
- Optimizing `.wasm` binary size
- Debugging and profiling WASM modules

## Quick Start

```bash
# Install tooling
rustup target add wasm32-unknown-unknown
cargo install wasm-pack

# Create project
cargo new --lib my-wasm-lib
cd my-wasm-lib
```

### Cargo.toml

```toml
[lib]
crate-type = ["cdylib", "rlib"]

[dependencies]
wasm-bindgen = "0.2"
web-sys = { version = "0.3", features = ["console", "Document", "Element", "Window"] }
js-sys = "0.3"

[profile.release]
opt-level = "s"     # optimize for size
lto = true
```

### src/lib.rs

```rust
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub fn greet(name: &str) -> String {
    format!("Hello, {name}!")
}

#[wasm_bindgen]
pub struct Universe {
    width: u32,
    height: u32,
    cells: Vec<u8>,
}

#[wasm_bindgen]
impl Universe {
    pub fn new(width: u32, height: u32) -> Self {
        Self { width, height, cells: vec![0; (width * height) as usize] }
    }

    pub fn tick(&mut self) { /* game of life step */ }

    pub fn cells_ptr(&self) -> *const u8 { self.cells.as_ptr() }
}
```

### Build & Use

```bash
wasm-pack build --target web      # for <script type="module">
wasm-pack build --target bundler  # for webpack/vite
wasm-pack build --target nodejs   # for Node.js
```

## wasm-bindgen Attributes

### Exporting to JS

```rust
#[wasm_bindgen]
pub fn add(a: u32, b: u32) -> u32 { a + b }

#[wasm_bindgen]
pub struct Counter { count: u32 }

#[wasm_bindgen]
impl Counter {
    #[wasm_bindgen(constructor)]
    pub fn new() -> Self { Self { count: 0 } }

    pub fn increment(&mut self) { self.count += 1; }

    #[wasm_bindgen(getter)]
    pub fn count(&self) -> u32 { self.count }
}
```

### Importing from JS

```rust
#[wasm_bindgen]
extern "C" {
    fn alert(s: &str);

    #[wasm_bindgen(js_namespace = console)]
    fn log(s: &str);

    #[wasm_bindgen(js_namespace = Math)]
    fn random() -> f64;
}
```

### DOM Manipulation (web-sys)

```rust
use web_sys::{window, Document, Element};

pub fn create_element() -> Result<(), JsValue> {
    let document = window().unwrap().document().unwrap();
    let div = document.create_element("div")?;
    div.set_text_content(Some("Hello from Rust!"));
    document.body().unwrap().append_child(&div)?;
    Ok(())
}
```

## Size Optimization

```toml
# Cargo.toml
[profile.release]
opt-level = "z"       # aggressive size optimization
lto = true
codegen-units = 1
strip = true
```

```bash
# Further reduce with wasm-opt (from binaryen)
wasm-opt -Oz -o output.wasm input.wasm

# Analyze what's taking space
cargo install twiggy
twiggy top pkg/my_lib_bg.wasm
twiggy dominators pkg/my_lib_bg.wasm
```

Avoid:
- `format!` / `panic!` messages (pull in formatting machinery)
- `std::collections::HashMap` (large; use a simpler map)
- String-heavy APIs (prefer passing indices/pointers for hot paths)

## Deployment

```
wasm-pack build --target web --release
# Produces pkg/ directory with:
#   my_lib.js       — JS glue
#   my_lib_bg.wasm  — compiled module
#   my_lib.d.ts     — TypeScript definitions
```

Serve with correct MIME type: `application/wasm`

Use `WebAssembly.instantiateStreaming` for fastest loading.

## Reference Map

- `references/wasm-bindgen-api.md` — attributes, types, conversions
- `references/web-sys-dom.md` — DOM, events, fetch, canvas
- `references/optimization-debugging.md` — size profiling, debugging, testing

## Key References

- [Rust and WebAssembly](https://rustwasm.github.io/docs/book/)
- [wasm-bindgen Guide](https://rustwasm.github.io/docs/wasm-bindgen/)
- [wasm-pack](https://rustwasm.github.io/docs/wasm-pack/)
- [web-sys](https://rustwasm.github.io/wasm-bindgen/api/web_sys/)
