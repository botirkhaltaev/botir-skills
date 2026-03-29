---
name: rust-unsafe-ffi
description: Master unsafe Rust, FFI, and foreign function interface programming. Use when writing unsafe Rust code with raw pointers, interfacing with C/C++ libraries, creating bindings with bindgen, implementing zero-cost abstractions, working with uninitialized memory, or understanding the safety/unsafety boundary.
---

# Rust Unsafe & FFI Programming

Comprehensive guide to unsafe Rust and Foreign Function Interface (FFI) programming based on The Rustonomicon, Unsafe Code Guidelines, and bindgen documentation.

## When to Use This Skill

- Writing unsafe Rust code with raw pointers
- Interfacing with C/C++ libraries via FFI
- Creating Rust bindings to C/C++ libraries with bindgen
- Implementing zero-cost abstractions over unsafe code
- Working with uninitialized or untyped memory
- Understanding the boundary between safe and unsafe Rust
- Writing sound unsafe code that doesn't cause undefined behavior

## Core References

- [The Rustonomicon](https://doc.rust-lang.org/nomicon/) - The definitive guide to unsafe Rust
- [Unsafe Code Guidelines](https://rust-lang.github.io/unsafe-code-guidelines/) - What unsafe code can and cannot do
- [bindgen User Guide](https://rust-lang.github.io/rust-bindgen/) - Automatic FFI binding generation

## Unsafe Rust Fundamentals

### The Safety/Unsafety Boundary

Unsafe Rust exists because Rust's safety guarantees are static - they can't be satisfied for all possible programs. Unsafe code tells the compiler "I promise this is safe."

Unsafe operations (require `unsafe` block):
- Dereferencing raw pointers (`*const T`, `*mut T`)
- Calling unsafe functions or methods
- Implementing unsafe traits
- Accessing or modifying mutable static variables
- Writing inline assembly

### Raw Pointers

Raw pointers bypass lifetime/borrowing checks:
```rust
let num = 5;
let raw_ptr: *const i32 = &num;
let mut mutable = 10;
let raw_mut_ptr: *mut i32 = &mut mutable;

unsafe {
    println!("Value: {}", *raw_ptr);
    *raw_mut_ptr = 20;
}
```

Key differences from references:
- Can be null
- Not checked for aliasing
- Can point to invalid memory
- No lifetime tracking

### Creating Safe Abstractions

Wrap unsafe code in safe APIs:
```rust
pub struct Vec<T> {
    ptr: NonNull<T>,
    len: usize,
    capacity: usize,
}

impl<T> Vec<T> {
    pub fn push(&mut self, value: T) {
        if self.len == self.capacity {
            self.reallocate();
        }
        unsafe {
            std::ptr::write(self.ptr.as_ptr().add(self.len), value);
        }
        self.len += 1;
    }
}
```

## Exception Safety (Panic Safety)

### The Panic/Unwind Safety Problem

When panic occurs, stack is unwound - must ensure no leaked resources or half-initialized data:

```rust
struct Wrapper {
    data: ManuallyDrop<String>,
}

impl Drop for Wrapper {
    fn drop(&mut self) {
        unsafe { ManuallyDrop::drop(&mut self.data) };
    }
}
```

### Panic Safety Guarantees

| Level | Description |
|-------|-------------|
| No guarantee | May leak resources or corrupt data |
| Leak safe | No undefined behavior, but resources may leak |
| Unwind safe | No leaks, no corruption |
| Exception safe | Full safety including noexcept semantics |

Use `std::panic::catch_unwind` for fallible FFI:
```rust
std::panic::catch_unwind(|| {
    unsafe { some_ffi_call() }
})
```

## Working with Uninitialized Memory

### MaybeUninit

Always initialize before reading:
```rust
use std::mem::MaybeUninit;

let mut data: MaybeUninit<i32> = MaybeUninit::uninit();
// ... write to data ...
let value = unsafe { data.assume_init() };
```

For arrays of MaybeUninit:
```rust
let mut arr: [MaybeUninit<i32>; 10] = [MaybeUninit::uninit(); 10];
arr[0].write(42);
```

### Zeroing Memory

Never use `std::mem::zeroed()` for non-Copy types:
```rust
use std::mem::MaybeUninit;

let mut data: MaybeUninit<String> = MaybeUninit::uninit();
// zeroed() is undefined behavior for String!
```

## FFI (Foreign Function Interface)

### Calling C Functions

```rust
use std::ffi::CString;

#[link(name = "c")]
extern "C" {
    fn printf(format: *const libc::c_char) -> libc::c_int;
}

let msg = CString::new("Hello %s\n").unwrap();
unsafe {
    printf(msg.as_ptr());
}
```

### Using bindgen for Automatic Bindings

Add to `Cargo.toml`:
```toml
[build-dependencies]
bindgen = "0.69"

[dependencies]
libc = "0.2"
```

Create `build.rs`:
```rust
fn main() {
    bindgen::builder()
        .header("wrapper.h")
        .parse_callbacks(Box::new(bindgen::CargoCallbacks))
        .generate()
        .expect("Unable to generate bindings")
        .write_to_file("src/bindings.rs")
        .expect("Couldn't write bindings!");
}
```

Run `cargo build` to generate bindings.

### Common FFI Patterns

#### Passing Strings
```rust
let c_string = CString::new("rust").unwrap();
unsafe { some_c_function(c_string.as_ptr()) }

unsafe {
    let ptr = receive_c_string();
    let rust_string = CString::from_raw(ptr).into_string().unwrap();
}
```

#### Error Handling Across FFI
```rust
#[repr(C)]
pub struct FfiResult {
    pub success: bool,
    pub error_code: i32,
    pub data: *mut std::ffi::c_void,
}
```

## Data Layout

### Size, Alignment, and Padding

```rust
#[repr(C)] // C-compatible layout
struct Data {
    a: u8,   // offset 0
    _pad0: [u8; 3],
    b: u32,  // offset 4
    c: u16,  // offset 8
    _pad1: [u8; 2],
} // size = 12

#[repr(packed)] // No padding
struct Packed {
    a: u8,
    b: u32,
    c: u16,
} // size = 7
```

### Type Punning

```rust
union IntOrFloat {
    int: u32,
    float: f32,
}

let mut value = IntOrFloat { int: 42 };
let float_val = unsafe { value.float };
```

## Soundness Checklist

When writing unsafe code:
- [ ] All unsafe blocks have safety comments explaining why they're safe
- [ ] Raw pointers are never dereferenced outside unsafe blocks
- [ ] References are never created from invalid pointers
- [ ] Data races are prevented (use proper synchronization)
- [ ] Memory is properly initialized before reading
- [ ] Memory is not used after being freed
- [ ] Panic safety is considered for allocations
- [ ] FFI boundaries handle all error cases

## Reference Map

- `references/ffi-basics.md` - Raw pointers, unsafe blocks, safe abstractions
- `references/ffi-bindgen.md` - Using bindgen for C/C++ bindings
- `references/memory-layout.md` - Size, alignment, padding, type punning
- `references/exception-safety.md` - Panic safety, unwind guarantees

## Key References

- [The Rustonomicon](https://doc.rust-lang.org/nomicon/) - Full unsafe Rust guide
- [Unsafe Code Guidelines](https://rust-lang.github.io/unsafe-code-guidelines/) - Reference
- [bindgen Book](https://rust-lang.github.io/rust-bindgen/) - FFI bindings
- [libc crate](https://docs.rs/libc/) - Unix/POSIX bindings
- [std::ffi](https://doc.rust-lang.org/std/ffi/) - FFI utilities
