# Peripherals & HAL

## Singleton Peripherals

Hardware peripherals are singletons — only one instance exists:

```rust
use stm32f3xx_hal::pac;

let dp = pac::Peripherals::take().unwrap();  // can only be called once
let cp = cortex_m::Peripherals::take().unwrap();

let mut rcc = dp.RCC.constrain();
let mut gpioa = dp.GPIOA.split(&mut rcc.ahb);
```

## GPIO

```rust
use stm32f3xx_hal::gpio::{Output, PushPull, Input, PullUp};

// Configure pin as output
let mut led = gpioa.pa5.into_push_pull_output(&mut gpioa.moder, &mut gpioa.otyper);
led.set_high().unwrap();
led.set_low().unwrap();
led.toggle().unwrap();

// Configure pin as input with pull-up
let button = gpioa.pa0.into_pull_up_input(&mut gpioa.moder, &mut gpioa.pupdr);
if button.is_high().unwrap() { /* button not pressed */ }
```

## Timers

```rust
use stm32f3xx_hal::timer::Timer;
use fugit::ExtU32;

let mut timer = Timer::new(dp.TIM2, 1.Hz(), &mut rcc.apb1);
timer.listen(Event::Update);  // enable interrupt

// Blocking delay
let mut delay = Timer::new(dp.TIM6, &clocks);
delay.delay_ms(500u32);
```

## I2C

```rust
use stm32f3xx_hal::i2c::I2c;

let scl = gpiob.pb6.into_af_open_drain(&mut gpiob.moder, &mut gpiob.otyper, &mut gpiob.afrl);
let sda = gpiob.pb7.into_af_open_drain(&mut gpiob.moder, &mut gpiob.otyper, &mut gpiob.afrl);

let mut i2c = I2c::new(dp.I2C1, (scl, sda), 400.kHz(), clocks, &mut rcc.apb1);

// Write then read
let mut buf = [0u8; 6];
i2c.write_read(0x68, &[0x3B], &mut buf).unwrap();
```

## SPI

```rust
use stm32f3xx_hal::spi::Spi;

let sck = gpioa.pa5.into_af_push_pull(...);
let miso = gpioa.pa6.into_af_push_pull(...);
let mosi = gpioa.pa7.into_af_push_pull(...);

let mut spi = Spi::new(dp.SPI1, (sck, miso, mosi), mode, 1.MHz(), clocks, &mut rcc.apb2);

let mut rx_buf = [0u8; 4];
spi.transfer(&mut rx_buf, &[0x9F, 0, 0, 0]).unwrap();
```

## UART / Serial

```rust
use stm32f3xx_hal::serial::Serial;

let tx = gpioa.pa2.into_af_push_pull(...);
let rx = gpioa.pa3.into_af_push_pull(...);

let mut serial = Serial::new(dp.USART2, (tx, rx), 115_200.bps(), clocks, &mut rcc.apb1);

serial.write(b'H').unwrap();
let byte = nb::block!(serial.read()).unwrap();
```

## DMA

```rust
use stm32f3xx_hal::dma::{dma1, Transfer, Event};

let channels = dma1::Channels::new(dp.DMA1, &mut rcc.ahb);
let buf = cortex_m::singleton!(: [u8; 256] = [0; 256]).unwrap();

let transfer = serial.rx.read_dma(buf, channels.ch5);
// Transfer runs in background; CPU is free
let (buf, rx, ch) = transfer.wait();
```

## PWM

```rust
let mut pwm = Timer::new(dp.TIM3, &clocks).pwm(pin, 1.kHz());
pwm.set_duty(pwm.get_max_duty() / 2);  // 50% duty cycle
pwm.enable();
```

## ADC (Analog to Digital)

```rust
let mut adc = Adc::new(dp.ADC1, &mut rcc.ahb, &clocks);
let mut pin = gpioa.pa0.into_analog(&mut gpioa.moder, &mut gpioa.pupdr);

let sample: u16 = adc.read(&mut pin).unwrap();
let voltage = sample as f32 * 3.3 / 4096.0;
```
