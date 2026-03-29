# Interoperability

## C FFI

```rust
#[repr(C)]
pub struct CData {
    pub value: libc::c_int,
    pub ptr: *mut libc::c_void,
}
```

## Send + Sync

```rust
unsafe impl Send for MyType {}
unsafe impl Sync for MyType {}
```

## No_std Compatible

```rust
#![no_std]

extern crate alloc;

// Use alloc crates
use alloc::vec::Vec;
```
