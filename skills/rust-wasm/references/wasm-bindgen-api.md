# wasm-bindgen API Reference

## Supported Types

| Rust Type | JS Type | Notes |
|-----------|---------|-------|
| `u8`..`u64`, `i8`..`i64` | `number` / `BigInt` | u64/i64 → BigInt |
| `f32`, `f64` | `number` | |
| `bool` | `boolean` | |
| `String` | `string` | Copies across boundary |
| `&str` | `string` | Temporary; JS gets a view |
| `Vec<u8>` | `Uint8Array` | Copies |
| `Box<[u8]>` | `Uint8Array` | Transfers ownership |
| `JsValue` | any | Opaque JS value |
| `Option<T>` | `T \| undefined` | |
| `Result<T, JsValue>` | `T` (throws on Err) | |

## Passing Complex Data

### Via serde

```rust
use serde::{Serialize, Deserialize};
use wasm_bindgen::prelude::*;

#[derive(Serialize, Deserialize)]
pub struct Config { pub name: String, pub count: u32 }

#[wasm_bindgen]
pub fn process_config(val: JsValue) -> Result<JsValue, JsValue> {
    let config: Config = serde_wasm_bindgen::from_value(val)?;
    let result = do_work(&config);
    Ok(serde_wasm_bindgen::to_value(&result)?)
}
```

### Via shared memory (zero-copy for hot paths)

```rust
#[wasm_bindgen]
impl Simulation {
    pub fn data_ptr(&self) -> *const f64 { self.data.as_ptr() }
    pub fn data_len(&self) -> usize { self.data.len() }
}
```

JS side:
```js
const ptr = sim.data_ptr();
const len = sim.data_len();
const view = new Float64Array(wasm.memory.buffer, ptr, len);
// Read directly from WASM memory — zero copy
```

## Closures

```rust
use wasm_bindgen::closure::Closure;

// Closure passed to JS (must be explicitly freed or leaked)
let cb = Closure::wrap(Box::new(move |event: web_sys::Event| {
    // handle event
}) as Box<dyn FnMut(web_sys::Event)>);

element.add_event_listener_with_callback("click", cb.as_ref().unchecked_ref())?;
cb.forget();  // leak — lives for page lifetime
```

For short-lived closures:

```rust
let cb = Closure::once(move || { /* one-shot */ });
```

## async/await in WASM

```rust
use wasm_bindgen_futures::spawn_local;

#[wasm_bindgen]
pub async fn fetch_data(url: String) -> Result<JsValue, JsValue> {
    let resp = reqwest::get(&url).await.map_err(|e| JsValue::from_str(&e.to_string()))?;
    let json = resp.json::<serde_json::Value>().await.map_err(|e| JsValue::from_str(&e.to_string()))?;
    Ok(serde_wasm_bindgen::to_value(&json)?)
}

// Or spawn without returning
#[wasm_bindgen(start)]
pub fn main() {
    spawn_local(async {
        // async initialization
    });
}
```

## Error Handling

```rust
#[wasm_bindgen]
pub fn might_fail(input: &str) -> Result<String, JsValue> {
    if input.is_empty() {
        return Err(JsValue::from_str("input cannot be empty"));
    }
    Ok(format!("processed: {input}"))
}
// JS: try { result = might_fail("") } catch (e) { console.error(e) }
```

## TypeScript Definitions

wasm-bindgen auto-generates `.d.ts` files:

```typescript
// Generated: my_lib.d.ts
export function greet(name: string): string;
export class Counter {
    constructor();
    increment(): void;
    readonly count: number;
}
```

Custom TypeScript annotations:

```rust
#[wasm_bindgen(typescript_custom_section)]
const TS_APPEND: &str = r#"
export interface Options {
    verbose?: boolean;
    output?: string;
}
"#;
```
