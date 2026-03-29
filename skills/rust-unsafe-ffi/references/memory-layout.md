# Memory Layout

## Size and Alignment

```rust
#[repr(C)]
struct Example {
    a: u8,    // size 1, align 1
    b: u32,   // size 4, align 4 -> offset 4
    c: u16,   // size 2, align 2 -> offset 8
}
// Total size: 12 (with padding)
```

## Packed Structures

```rust
#[repr(packed)]
struct Packed {
    a: u8,
    b: u32,
    c: u16,
}
// Total size: 7 (no padding)
```

## Type Punning

Via union:
```rust
union IntOrFloat {
    int: u32,
    float: f32,
}
```

Via transmute:
```rust
let bytes: [u8; 4] = [0, 0, 160, 66];
let num: f32 = unsafe { std::mem::transmute(bytes) };
```

## Layout Computation

```rust
use std::mem::size_of;
use std::mem::align_of;

size_of::<u32>();   // 4
align_of::<u32>();  // 4
```
