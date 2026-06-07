---
name: rust-embedded
description: Embedded Rust development for bare-metal and no_std environments. Use when targeting microcontrollers, writing HAL drivers, working with peripherals, handling interrupts, or using RTIC/Embassy for real-time systems.
---

# Rust Embedded Development

Based on The Embedded Rust Book, The Embedonomicon, and Embedded Rust on Espressif.

## When to Use This Skill

- Writing `#![no_std]` applications for microcontrollers
- Working with HAL (Hardware Abstraction Layer) crates
- Configuring linker scripts and memory layouts
- Handling interrupts and DMA
- Using RTIC or Embassy for concurrency
- Targeting ARM Cortex-M, RISC-V, or ESP32

## Project Structure

```
my-firmware/
├── .cargo/config.toml     # target, linker settings
├── Cargo.toml
├── memory.x               # memory layout (flash + RAM regions)
├── build.rs               # optional: extra link args
└── src/
    └── main.rs            # #![no_std] #![no_main]
```

## Minimal no_std Binary

```rust
#![no_std]
#![no_main]

use panic_halt as _;  // panic handler (halt on panic)
use cortex_m_rt::entry;

#[entry]
fn main() -> ! {
    // Initialize peripherals
    let dp = pac::Peripherals::take().unwrap();
    let mut led = dp.GPIOA.pin(5).into_push_pull_output();

    loop {
        led.toggle();
        cortex_m::asm::delay(8_000_000);
    }
}
```

## Memory Layout (memory.x)

```
MEMORY
{
    FLASH : ORIGIN = 0x08000000, LENGTH = 256K
    RAM   : ORIGIN = 0x20000000, LENGTH = 64K
}
```

## .cargo/config.toml

```toml
[target.thumbv7em-none-eabihf]
runner = "probe-rs run --chip STM32F303VCTx"

[build]
target = "thumbv7em-none-eabihf"

[env]
DEFMT_LOG = "info"
```

## HAL Layer Architecture

```
┌─────────────────────────────┐
│  Application Code           │
├─────────────────────────────┤
│  HAL (e.g. stm32f3xx-hal)  │  ← high-level, safe API
├─────────────────────────────┤
│  PAC (e.g. stm32f3)        │  ← auto-generated register access
├─────────────────────────────┤
│  Hardware                   │
└─────────────────────────────┘
```

- **PAC** (Peripheral Access Crate): Generated from SVD files, provides raw register access
- **HAL**: Type-safe wrappers implementing `embedded-hal` traits
- **BSP** (Board Support Package): Board-specific pin mappings and initialization

## embedded-hal Traits

Portable driver abstraction:

```rust
use embedded_hal::digital::OutputPin;
use embedded_hal::i2c::I2c;
use embedded_hal::spi::SpiDevice;
use embedded_hal::delay::DelayNs;

// Write a portable driver
pub struct Sensor<I2C> {
    i2c: I2C,
    addr: u8,
}

impl<I2C: I2c> Sensor<I2C> {
    pub fn read_temp(&mut self) -> Result<f32, I2C::Error> {
        let mut buf = [0u8; 2];
        self.i2c.write_read(self.addr, &[0x00], &mut buf)?;
        Ok(i16::from_be_bytes(buf) as f32 * 0.0625)
    }
}
```

## Interrupts

```rust
use cortex_m::peripheral::NVIC;
use stm32f3xx_hal::pac::interrupt;

#[interrupt]
fn TIM2() {
    // Timer interrupt handler
    // Access shared state via critical section or RTIC resources
    cortex_m::interrupt::free(|cs| {
        if let Some(timer) = TIMER.borrow(cs).borrow_mut().as_mut() {
            timer.clear_interrupt(Event::Update);
        }
    });
}
```

## RTIC (Real-Time Interrupt-driven Concurrency)

```rust
#[rtic::app(device = stm32f3xx_hal::pac, dispatchers = [SPI1])]
mod app {
    use super::*;

    #[shared]
    struct Shared { led: Led }

    #[local]
    struct Local { timer: Timer }

    #[init]
    fn init(cx: init::Context) -> (Shared, Local) {
        // Setup peripherals
        (Shared { led }, Local { timer })
    }

    #[task(binds = TIM2, local = [timer], shared = [led])]
    fn tick(cx: tick::Context) {
        cx.local.timer.clear_interrupt();
        cx.shared.led.lock(|led| led.toggle());
    }
}
```

## Key Crates

| Crate | Purpose |
|-------|---------|
| `cortex-m` | Low-level ARM Cortex-M access |
| `cortex-m-rt` | Runtime (vector table, entry) |
| `panic-halt` | Panic handler: infinite loop |
| `defmt` | Efficient logging for embedded |
| `probe-rs` | Flashing and debugging tool |
| `embedded-hal` | Hardware abstraction traits |
| `rtic` | Real-time concurrency framework |
| `embassy` | Async embedded framework |

## Reference Map

- `references/no-std-basics.md` — no_std setup, panic handlers, alloc
- `references/peripherals-hal.md` — GPIO, timers, I2C, SPI, UART
- `references/interrupts-concurrency.md` — NVIC, critical sections, RTIC, Embassy

## Key References

- [The Embedded Rust Book](https://docs.rust-embedded.org/book/)
- [The Embedonomicon](https://docs.rust-embedded.org/embedonomicon/)
- [RTIC](https://rtic.rs)
- [Embassy](https://embassy.dev)
