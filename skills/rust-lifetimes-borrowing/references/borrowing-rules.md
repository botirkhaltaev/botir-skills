# Borrowing Rules & Mechanics

## The Two Rules

1. At any point in time, you can have **either** one mutable reference OR any number of immutable references.
2. References must always be valid (no dangling pointers).

## Reborrowing

Creating a new borrow from an existing one:

```rust
fn takes_ref(s: &str) {}

let mut s = String::from("hello");
let r: &mut String = &mut s;
takes_ref(&*r);    // reborrow: &mut String → &String → &str
r.push_str("!");   // original &mut still usable after reborrow ends
```

## Two-Phase Borrows

The compiler allows a brief shared-borrow phase during method resolution:

```rust
let mut v = vec![1, 2, 3];
v.push(v.len());  // v.len() borrows &v; .push() borrows &mut v
// This works because the compiler uses two-phase borrows
```

## Non-Lexical Lifetimes (NLL)

Borrows end at the point of **last use**, not at the end of the enclosing `{}`:

```rust
let mut data = vec![1, 2, 3];
let first = &data[0];
println!("{first}");     // last use of `first`
data.push(4);            // &mut borrow starts here — no conflict
```

## Splitting Borrows

You can borrow disjoint parts of a struct simultaneously:

```rust
struct State { name: String, count: u32 }
let mut s = State { name: "x".into(), count: 0 };
let n = &mut s.name;
let c = &mut s.count;  // different fields — compiler allows both
n.push_str("y");
*c += 1;
```

For slices, use `split_at_mut`:

```rust
let mut arr = [1, 2, 3, 4];
let (left, right) = arr.split_at_mut(2);
left[0] = 10;
right[0] = 30;
```

## Common Borrow Checker Patterns

### Temporary borrow in a loop

```rust
let mut map = HashMap::new();
for key in keys {
    // entry API avoids double-lookup and borrow issues
    map.entry(key).or_insert_with(|| compute(key));
}
```

### Borrow ends before mutation

```rust
let mut v = vec![1, 2, 3];
let len = v.len();   // immutable borrow of v ends here
v.truncate(len - 1); // mutable borrow starts — fine
```

### Collecting to release borrow

```rust
let mut map: HashMap<String, Vec<String>> = HashMap::new();
let keys: Vec<_> = map.keys().cloned().collect(); // release &map
for key in keys {
    map.get_mut(&key).unwrap().push("new".into());
}
```

## Interior Mutability Escape Hatches

When the borrow checker is too restrictive for safe patterns:

- `Cell<T>` — for `Copy` types; get/set without `&mut`
- `RefCell<T>` — runtime-checked `&` → `&mut`; panics on violation
- `Mutex<T>` / `RwLock<T>` — thread-safe equivalents

These are safe but shift checking from compile-time to runtime.
