# Exception Safety

## The Problem

When panic occurs during stack unwinding, code may be left in inconsistent state.

## Panic Safety Levels

| Level | Guarantee |
|-------|-----------|
| No guarantee | Undefined behavior possible |
| Leak safe | No UB, but resources may leak |
| Unwind safe | No leaks, data consistent |
| Exception safe | Full guarantee |

## Using ManuallyDrop

```rust
use std::mem::ManuallyDrop;

struct Data {
    resource: Resource,
}

impl Drop for Data {
    fn drop(&mut self) {
        // Prevent double-free during unwinding
        let this = ManuallyDrop::new(self);
        unsafe {
            ManuallyDrop::drop(&mut this.resource);
        }
    }
}
```

## Catch Unwind

```rust
use std::panic::catch_unwind;

let result = catch_unwind(|| {
    unsafe { some_ffi_call() }
});

match result {
    Ok(value) => /* success */,
    Err(e) => /* panic occurred */,
}
```

## AssertUnwindSafe

```rust
use std::panic::{catch_unwind, AssertUnwindSafe};

let data = AssertUnwindSafe(mut_data);
let result = catch_unwind(move || {
    data.do_something();
});
```
