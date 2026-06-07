# Ownership & Move Semantics

## Stack vs Heap

- Stack: fixed-size, LIFO, fast allocation (push/pop). Stores `i32`, `f64`, tuples of `Copy` types.
- Heap: dynamic-size, pointer-based, slower allocation. Stores `String`, `Vec<T>`, `Box<T>`.

## Move Semantics

Assignment or passing to a function **moves** ownership for non-`Copy` types:

```rust
let s = String::from("hello");
let t = s;  // s is moved; s is no longer valid
take_ownership(t);  // t is moved into the function
```

After a move, the original binding is invalid. The compiler enforces this statically.

## Copy vs Clone

- `Copy`: bitwise copy; type must be entirely stack-allocated (no heap pointers). Implements `Copy` trait. Examples: `i32`, `f64`, `bool`, `char`, `(i32, i32)`.
- `Clone`: explicit deep copy. Must call `.clone()`. Heap types implement `Clone` but not `Copy`.

```rust
#[derive(Copy, Clone)]
struct Point { x: f64, y: f64 }  // all fields are Copy → struct can be Copy

#[derive(Clone)]
struct Buffer { data: Vec<u8> }  // Vec is not Copy → Buffer cannot be Copy
```

## Drop Order

- Local variables are dropped in reverse declaration order.
- Struct fields are dropped in declaration order.
- `std::mem::drop(x)` drops early (equivalent to `{ x; }`).

```rust
struct Guard(&'static str);
impl Drop for Guard {
    fn drop(&mut self) { println!("dropping {}", self.0); }
}

fn main() {
    let _a = Guard("a");
    let _b = Guard("b");
}
// Output: "dropping b" then "dropping a"
```

## Partial Moves

Moving a field out of a struct makes the whole struct unusable, unless remaining fields are `Copy`:

```rust
struct Pair { name: String, age: u32 }
let p = Pair { name: "Alice".into(), age: 30 };
let n = p.name;  // partial move
// println!("{}", p.age);  // OK in recent Rust (age is Copy)
// println!("{}", p.name); // ERROR: field was moved
```

## Return Values Transfer Ownership

```rust
fn create() -> String { String::from("new") }
let s = create();  // ownership transferred to caller
```

## Moving Out of Collections

Use `.remove()`, `.pop()`, `.drain()`, or `std::mem::take()`:

```rust
let mut v = vec![String::from("a"), String::from("b")];
let first = v.remove(0);  // moves element out, shifts remaining
```
