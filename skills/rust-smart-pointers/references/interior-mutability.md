# Interior Mutability

## The Pattern

Interior mutability allows mutation through a shared reference (`&T`). The borrow rules are enforced differently:
- `Cell<T>` — no runtime cost; only for `Copy` types
- `RefCell<T>` — runtime borrow checking; panics on violation
- `Mutex<T>` / `RwLock<T>` — thread-safe; blocks on contention

## Cell<T>

No overhead; `get()` copies, `set()` replaces:

```rust
use std::cell::Cell;

struct Config {
    debug_mode: Cell<bool>,
    request_count: Cell<u64>,
}

impl Config {
    fn handle_request(&self) {  // note: &self
        self.request_count.set(self.request_count.get() + 1);
        if self.debug_mode.get() {
            println!("request #{}", self.request_count.get());
        }
    }
}
```

### Cell Methods

| Method | Description |
|--------|-------------|
| `get()` | Copy out the value (requires `T: Copy`) |
| `set(val)` | Replace the value |
| `replace(val)` | Replace and return old value |
| `take()` | Replace with Default, return old (requires `T: Default`) |
| `into_inner()` | Consume Cell, return value |

## RefCell<T>

Runtime borrow checking with panic on violation:

```rust
use std::cell::RefCell;

let data = RefCell::new(HashMap::new());

// Returns Ref<T> (like &T)
let reader = data.borrow();
println!("{:?}", reader.get("key"));
drop(reader);  // must drop before mutable borrow

// Returns RefMut<T> (like &mut T)
let mut writer = data.borrow_mut();
writer.insert("key", "value");
// writer auto-dropped at end of scope
```

### try_borrow / try_borrow_mut

Non-panicking alternatives:

```rust
match data.try_borrow_mut() {
    Ok(mut guard) => guard.push(item),
    Err(_) => eprintln!("already borrowed!"),
}
```

### Common Pattern: Rc<RefCell<T>>

```rust
use std::rc::Rc;
use std::cell::RefCell;

struct EventBus {
    listeners: Vec<Rc<RefCell<dyn Listener>>>,
}

impl EventBus {
    fn notify(&self, event: &Event) {
        for listener in &self.listeners {
            listener.borrow_mut().handle(event);
        }
    }
}
```

## OnceCell / OnceLock

Initialize once, then immutable:

```rust
use std::cell::OnceCell;      // single-threaded
use std::sync::OnceLock;      // multi-threaded

// Thread-safe lazy initialization
static CONFIG: OnceLock<Config> = OnceLock::new();

fn get_config() -> &'static Config {
    CONFIG.get_or_init(|| Config::from_env())
}

// Single-threaded
struct CachedComputation {
    input: String,
    result: OnceCell<Vec<u8>>,
}

impl CachedComputation {
    fn get_result(&self) -> &Vec<u8> {
        self.result.get_or_init(|| expensive_compute(&self.input))
    }
}
```

## LazyLock / LazyCell

Lazy initialization with a closure:

```rust
use std::sync::LazyLock;

static REGEX: LazyLock<Regex> = LazyLock::new(|| {
    Regex::new(r"\d{4}-\d{2}-\d{2}").unwrap()
});
```

## When to Use What

| Scenario | Use |
|----------|-----|
| Simple counter/flag behind `&self` | `Cell<T>` |
| Complex type behind `&self` (single thread) | `RefCell<T>` |
| Shared ownership + mutation (single thread) | `Rc<RefCell<T>>` |
| Shared ownership + mutation (multi thread) | `Arc<Mutex<T>>` |
| Many readers, few writers (multi thread) | `Arc<RwLock<T>>` |
| One-time initialization | `OnceCell` / `OnceLock` |
| Lazy global constant | `LazyLock` |

## Anti-Pattern: RefCell Everywhere

If you find yourself wrapping many fields in `RefCell`, it usually means the ownership structure needs redesigning. Consider:
1. Restructuring to pass `&mut self`
2. Using indices instead of references
3. Using an ECS pattern for complex graphs
4. Using channels for message passing
