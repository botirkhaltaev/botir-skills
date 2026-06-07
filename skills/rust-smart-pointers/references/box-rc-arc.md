# Box, Rc, and Arc

## Box<T> Details

### When to Use Box

1. **Recursive types** — must have known size at compile time
2. **Large values** — avoid stack overflow for big data
3. **Trait objects** — `Box<dyn Trait>` for dynamic dispatch
4. **Transfer ownership** — without copying large data

### Box::leak

Convert Box into a `'static` reference:

```rust
let config: &'static Config = Box::leak(Box::new(Config::load()));
// Lives for the entire program — useful for global state
// Memory is never freed (unless you reconstruct the Box)
```

### Box<[T]> vs Vec<T>

```rust
let v: Vec<u8> = vec![0; 1024];
let boxed_slice: Box<[u8]> = v.into_boxed_slice();
// Box<[u8]> is smaller: just (pointer, length) — no capacity field
// Cannot grow without converting back to Vec
```

## Rc<T> Details

### Rc::try_unwrap

Get owned value if ref count is 1:

```rust
let rc = Rc::new(String::from("hello"));
match Rc::try_unwrap(rc) {
    Ok(string) => println!("got owned: {string}"),
    Err(rc) => println!("still shared: {}", Rc::strong_count(&rc)),
}
```

### Rc::make_mut (Clone-on-Write)

```rust
let mut rc = Rc::new(vec![1, 2, 3]);
// If uniquely owned, returns &mut; otherwise clones
Rc::make_mut(&mut rc).push(4);
```

### Rc + RefCell Pattern

Shared mutable state (single-threaded):

```rust
use std::cell::RefCell;
use std::rc::Rc;

type SharedState = Rc<RefCell<AppState>>;

fn modify_state(state: &SharedState) {
    state.borrow_mut().counter += 1;
}

// Tree with parent back-references
struct TreeNode {
    value: i32,
    children: Vec<Rc<RefCell<TreeNode>>>,
    parent: Weak<RefCell<TreeNode>>,
}
```

## Arc<T> Details

### Arc vs Rc Performance

Arc uses atomic operations for reference counting — slightly slower than Rc's non-atomic ops. Only use Arc when sharing across threads.

### Arc + Mutex Pattern

```rust
use std::sync::{Arc, Mutex};

struct SharedCache {
    data: Arc<Mutex<HashMap<String, String>>>,
}

impl SharedCache {
    fn get(&self, key: &str) -> Option<String> {
        self.data.lock().unwrap().get(key).cloned()
    }

    fn insert(&self, key: String, value: String) {
        self.data.lock().unwrap().insert(key, value);
    }
}
```

### Arc + RwLock (Multiple Readers)

```rust
use std::sync::{Arc, RwLock};

let config = Arc::new(RwLock::new(Config::default()));

// Multiple readers simultaneously
let reader = config.read().unwrap();
println!("{:?}", reader.port);

// Exclusive writer
let mut writer = config.write().unwrap();
writer.port = 9090;
```

## Weak References

### Purpose: Break Reference Cycles

```rust
// Without Weak: cycle leaks memory
struct A { b: Rc<B> }
struct B { a: Rc<A> }  // LEAK: A → B → A → B → ...

// With Weak: back-references don't prevent deallocation
struct A { b: Rc<B> }
struct B { a: Weak<A> }  // weak ref doesn't keep A alive
```

### Weak API

```rust
let strong = Rc::new(42);
let weak: Weak<i32> = Rc::downgrade(&strong);

assert_eq!(weak.strong_count(), 1);
assert_eq!(weak.weak_count(), 1);

// Upgrade: Weak → Option<Rc>
assert_eq!(*weak.upgrade().unwrap(), 42);

drop(strong);
assert!(weak.upgrade().is_none());  // value was deallocated
```

## Memory Layout

```
Box<T>:     [ptr] → [T data]
Rc<T>:      [ptr] → [strong_count | weak_count | T data]
Arc<T>:     [ptr] → [atomic_strong | atomic_weak | T data]
```

All are one pointer wide (8 bytes on 64-bit). The allocation includes the control block.
