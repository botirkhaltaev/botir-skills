# no_std Basics

## What #![no_std] Means

Removes the standard library (`std`). You still get `core` (no heap, no OS) and optionally `alloc` (heap without OS).

Available without std:
- Primitive types, slices, arrays
- `Option`, `Result`, iterators
- `core::fmt`, `core::ops`
- Atomic types, `core::sync`

NOT available:
- `println!`, `String`, `Vec` (without `alloc`)
- `std::fs`, `std::net`, `std::io`
- `std::thread`, `std::time`
- `HashMap` (without `alloc` + `hashbrown`)

## Using alloc Without std

```rust
#![no_std]
extern crate alloc;

use alloc::vec::Vec;
use alloc::string::String;
use alloc::boxed::Box;

// Must provide a global allocator
use embedded_alloc::LlffHeap as Heap;

#[global_allocator]
static HEAP: Heap = Heap::empty();

fn init_heap() {
    const HEAP_SIZE: usize = 4096;
    static mut HEAP_MEM: [u8; HEAP_SIZE] = [0; HEAP_SIZE];
    unsafe { HEAP.init(HEAP_MEM.as_ptr() as usize, HEAP_SIZE) }
}
```

## Panic Handlers

Every no_std binary needs exactly one `#[panic_handler]`:

```rust
// Option 1: halt (production)
use panic_halt as _;

// Option 2: defmt + probe (development)
use defmt_rtt as _;
use panic_probe as _;

// Option 3: custom
#[panic_handler]
fn panic(info: &core::panic::PanicInfo) -> ! {
    // Log via defmt, blink LED, etc.
    loop { cortex_m::asm::bkpt() }
}
```

## Logging with defmt

Efficient structured logging via RTT (Real-Time Transfer):

```rust
use defmt::{info, warn, error, debug};

info!("Temperature: {} C", temp);
warn!("Buffer {=usize} nearly full", buf.len());
error!("Failed to read sensor: {}", Debug2Format(&err));
```

Requires: `defmt`, `defmt-rtt` (transport), `panic-probe` (panic handler).

## Conditional Compilation

```rust
// In lib.rs — support both std and no_std
#![cfg_attr(not(feature = "std"), no_std)]

#[cfg(feature = "std")]
extern crate std;

#[cfg(not(feature = "std"))]
extern crate alloc;

#[cfg(feature = "std")]
use std::vec::Vec;

#[cfg(not(feature = "std"))]
use alloc::vec::Vec;
```

In Cargo.toml:
```toml
[features]
default = ["std"]
std = []
```

## Build Targets

```bash
# List available targets
rustup target list | grep thumb

# Common targets
rustup target add thumbv6m-none-eabi      # Cortex-M0/M0+
rustup target add thumbv7em-none-eabihf   # Cortex-M4F/M7F (hard float)
rustup target add riscv32imc-unknown-none-elf  # RISC-V
```

## Linker Scripts

The `cortex-m-rt` crate expects `memory.x` to define memory regions:

```
MEMORY
{
    FLASH (rx)  : ORIGIN = 0x08000000, LENGTH = 512K
    RAM   (rwx) : ORIGIN = 0x20000000, LENGTH = 128K
}
```

For custom sections (DMA buffers, persistent data):

```
SECTIONS
{
    .dma_buffers (NOLOAD) : {
        *(.dma_buffers)
    } > RAM
}
```

```rust
#[link_section = ".dma_buffers"]
static mut DMA_BUF: [u8; 256] = [0; 256];
```
