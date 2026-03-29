# FFI Basics

## Raw Pointers

Raw pointers come in two forms: `*const T` (immutable) and `*mut T` (mutable). They bypass Rust's safety checks.

```rust
let mut value = 42;
let ptr: *const i32 = &value;
let mut_ptr: *mut i32 = &mut value;
```

### Dereferencing

Must be in unsafe block:
```rust
unsafe {
    let val = *ptr;
    *mut_ptr = 100;
}
```

### Creating Pointers

From references:
```rust
let x = 10;
let px: *const i32 = &x;
let py: *mut i32 = &mut x;
```

From address:
```rust
let addr = 0x1000 as *mut i32;
unsafe { *addr = 42; }
```

## Unsafe Functions

```rust
unsafe fn dangerous_operation(ptr: *mut i32) -> i32 {
    *ptr
}
```

### Safety Comments

Always document why unsafe is safe:
```rust
/// Writes value to pointer, caller must ensure ptr is valid.
/// 
/// # Safety
/// - ptr must be non-null and properly aligned
/// - ptr must point to initialized, writable memory
unsafe fn write_value(ptr: *mut i32, value: i32) {
    *ptr = value;
}
```

## Unsafe Traits

```rust
unsafe trait Foo {
    fn method(&self);
}

unsafe impl Foo for MyType {
    fn method(&self) { /* ... */ }
}
```
