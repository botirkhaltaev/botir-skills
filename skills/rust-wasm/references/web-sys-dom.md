# web-sys & DOM Interaction

## Feature Flags

web-sys uses feature flags for each Web API:

```toml
[dependencies.web-sys]
version = "0.3"
features = [
    "Window", "Document", "Element", "HtmlElement",
    "HtmlCanvasElement", "CanvasRenderingContext2d",
    "Event", "MouseEvent", "KeyboardEvent",
    "Request", "RequestInit", "Response", "Headers",
    "console",
]
```

## DOM Manipulation

```rust
use web_sys::{window, Document, Element, HtmlElement};
use wasm_bindgen::JsCast;

fn get_document() -> Document {
    window().unwrap().document().unwrap()
}

fn query(selector: &str) -> Option<Element> {
    get_document().query_selector(selector).ok().flatten()
}

fn create_list(items: &[&str]) -> Result<Element, JsValue> {
    let doc = get_document();
    let ul = doc.create_element("ul")?;

    for item in items {
        let li = doc.create_element("li")?;
        li.set_text_content(Some(item));
        ul.append_child(&li)?;
    }

    Ok(ul)
}
```

## Events

```rust
use wasm_bindgen::closure::Closure;
use web_sys::{Event, MouseEvent, HtmlElement};
use wasm_bindgen::JsCast;

fn setup_click_handler(element: &HtmlElement) {
    let closure = Closure::wrap(Box::new(move |event: MouseEvent| {
        let x = event.client_x();
        let y = event.client_y();
        web_sys::console::log_1(&format!("Click at ({x}, {y})").into());
    }) as Box<dyn FnMut(MouseEvent)>);

    element.add_event_listener_with_callback(
        "click",
        closure.as_ref().unchecked_ref(),
    ).unwrap();

    closure.forget();  // prevent deallocation
}
```

## Canvas 2D

```rust
use web_sys::{HtmlCanvasElement, CanvasRenderingContext2d};
use wasm_bindgen::JsCast;

fn draw(canvas: &HtmlCanvasElement) {
    let ctx = canvas
        .get_context("2d").unwrap().unwrap()
        .dyn_into::<CanvasRenderingContext2d>().unwrap();

    ctx.set_fill_style_str("rgb(200, 0, 0)");
    ctx.fill_rect(10.0, 10.0, 50.0, 50.0);

    ctx.begin_path();
    ctx.arc(75.0, 75.0, 50.0, 0.0, std::f64::consts::PI * 2.0).unwrap();
    ctx.stroke();
}
```

## Fetch API

```rust
use wasm_bindgen::JsCast;
use wasm_bindgen_futures::JsFuture;
use web_sys::{Request, RequestInit, Response};

pub async fn fetch_json(url: &str) -> Result<JsValue, JsValue> {
    let mut opts = RequestInit::new();
    opts.method("GET");

    let request = Request::new_with_str_and_init(url, &opts)?;
    request.headers().set("Accept", "application/json")?;

    let window = web_sys::window().unwrap();
    let resp_value = JsFuture::from(window.fetch_with_request(&request)).await?;
    let resp: Response = resp_value.dyn_into()?;

    let json = JsFuture::from(resp.json()?).await?;
    Ok(json)
}
```

## requestAnimationFrame Loop

```rust
use std::cell::RefCell;
use std::rc::Rc;
use wasm_bindgen::closure::Closure;

fn request_animation_frame(f: &Closure<dyn FnMut()>) {
    window().unwrap()
        .request_animation_frame(f.as_ref().unchecked_ref())
        .unwrap();
}

pub fn start_loop() {
    let f = Rc::new(RefCell::new(None));
    let g = f.clone();

    *g.borrow_mut() = Some(Closure::wrap(Box::new(move || {
        // Render frame
        render();
        // Schedule next frame
        request_animation_frame(f.borrow().as_ref().unwrap());
    }) as Box<dyn FnMut()>));

    request_animation_frame(g.borrow().as_ref().unwrap());
}
```

## Web Workers

```rust
use web_sys::Worker;

let worker = Worker::new("./worker.js")?;
worker.post_message(&JsValue::from_str("start"))?;
```

Worker side loads its own WASM instance for true parallelism.
