# Interrupts & Concurrency

## Critical Sections

Disable interrupts to safely access shared mutable state:

```rust
use cortex_m::interrupt;
use core::cell::RefCell;

static SHARED: interrupt::Mutex<RefCell<Option<Timer>>> =
    interrupt::Mutex::new(RefCell::new(None));

// In main
interrupt::free(|cs| {
    SHARED.borrow(cs).replace(Some(timer));
});

// In interrupt handler
#[interrupt]
fn TIM2() {
    interrupt::free(|cs| {
        if let Some(ref mut timer) = *SHARED.borrow(cs).borrow_mut() {
            timer.clear_interrupt(Event::Update);
        }
    });
}
```

## NVIC (Nested Vectored Interrupt Controller)

```rust
use cortex_m::peripheral::NVIC;
use stm32f3xx_hal::pac::Interrupt;

// Enable interrupt
unsafe { NVIC::unmask(Interrupt::TIM2) };

// Set priority (lower number = higher priority)
unsafe { cp.NVIC.set_priority(Interrupt::TIM2, 1) };

// Pend (trigger) an interrupt from software
NVIC::pend(Interrupt::TIM2);
```

## RTIC Framework

Zero-cost concurrency with priority-based preemption:

```rust
#[rtic::app(device = stm32f3xx_hal::pac, dispatchers = [SPI1])]
mod app {
    use super::*;

    #[shared]
    struct Shared {
        counter: u32,
    }

    #[local]
    struct Local {
        led: Led,
        timer: CounterHz<TIM2>,
    }

    #[init]
    fn init(cx: init::Context) -> (Shared, Local) {
        let dp = cx.device;
        // Configure clocks, peripherals...
        (Shared { counter: 0 }, Local { led, timer })
    }

    #[idle]
    fn idle(_cx: idle::Context) -> ! {
        loop { cortex_m::asm::wfi(); }  // sleep until interrupt
    }

    #[task(binds = TIM2, local = [timer], shared = [counter], priority = 2)]
    fn tick(mut cx: tick::Context) {
        cx.local.timer.clear_interrupt(Event::Update);
        cx.shared.counter.lock(|c| *c += 1);
        blink::spawn().ok();  // spawn software task
    }

    #[task(local = [led], priority = 1)]
    async fn blink(cx: blink::Context) {
        cx.local.led.toggle();
    }
}
```

Key RTIC concepts:
- **Resources**: Shared state with automatic lock-free access at equal priority
- **Priority ceiling**: Higher-priority tasks preempt lower ones
- **Dispatchers**: Free interrupt vectors used for software tasks
- **Zero-cost**: No runtime overhead; all scheduling determined at compile time

## Embassy (Async Embedded)

```rust
use embassy_executor::Spawner;
use embassy_stm32::gpio::{Level, Output, Speed};
use embassy_time::Timer;

#[embassy_executor::main]
async fn main(spawner: Spawner) {
    let p = embassy_stm32::init(Default::default());
    let mut led = Output::new(p.PA5, Level::Low, Speed::Low);

    spawner.spawn(background_task()).unwrap();

    loop {
        led.toggle();
        Timer::after_millis(500).await;
    }
}

#[embassy_executor::task]
async fn background_task() {
    loop {
        // Do periodic work
        Timer::after_secs(1).await;
    }
}
```

Embassy advantages:
- Familiar async/await syntax
- Cooperative multitasking without RTOS overhead
- Timer, channel, mutex primitives
- HAL with async support (I2C, SPI, UART)

## Shared Peripheral Access Patterns

### Mutex (critical-section based)

```rust
use critical_section::Mutex;
use core::cell::RefCell;

static SERIAL: Mutex<RefCell<Option<Serial>>> = Mutex::new(RefCell::new(None));

fn send_byte(byte: u8) {
    critical_section::with(|cs| {
        if let Some(ref mut serial) = *SERIAL.borrow_ref_mut(cs) {
            nb::block!(serial.write(byte)).ok();
        }
    });
}
```

### Passing Peripherals to Interrupt Handlers

Use `static mut` + critical section, or RTIC resources (preferred).

## Debugging

```bash
# Flash and run with defmt output
cargo run --release

# GDB debugging
probe-rs gdb --chip STM32F303VCTx
# In another terminal:
arm-none-eabi-gdb -x openocd.gdb target/thumbv7em-none-eabihf/release/firmware
```
