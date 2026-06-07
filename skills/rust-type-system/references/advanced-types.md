# Advanced Type System Features

## Type Aliases

```rust
type Result<T> = std::result::Result<T, Box<dyn std::error::Error>>;
type Callback = Box<dyn Fn(Event) -> Response + Send>;
type Matrix4x4 = [[f32; 4]; 4];
```

## Never Type (!)

```rust
fn diverge() -> ! {
    loop { /* never returns */ }
}

fn exit_process(code: i32) -> ! {
    std::process::exit(code)
}

// Useful in match arms
let val = match input.parse::<u32>() {
    Ok(n) => n,
    Err(_) => return Err(Error::Parse),  // ! coerces to any type
};
```

## Existential Types (impl Trait)

The caller doesn't know the concrete type:

```rust
fn make_counter() -> impl FnMut() -> u32 {
    let mut count = 0;
    move || { count += 1; count }
}

// Type-alias impl trait (TAIT) — nightly
type MyFuture = impl Future<Output = String>;
fn make_future() -> MyFuture { async { "hello".into() } }
```

## Type-Level Programming with PhantomData

### Unit Types as Tags

```rust
struct Meters;
struct Kilometers;

struct Distance<Unit> {
    value: f64,
    _unit: PhantomData<Unit>,
}

impl Distance<Meters> {
    fn to_km(self) -> Distance<Kilometers> {
        Distance { value: self.value / 1000.0, _unit: PhantomData }
    }
}

// Cannot add Distance<Meters> + Distance<Kilometers> without explicit conversion
```

### Type-Level State Machines

```rust
struct Open;
struct Closed;
struct HalfOpen;

struct CircuitBreaker<State> {
    failure_count: u32,
    _state: PhantomData<State>,
}

impl CircuitBreaker<Closed> {
    fn trip(self) -> CircuitBreaker<Open> { ... }
}

impl CircuitBreaker<Open> {
    fn half_open(self) -> CircuitBreaker<HalfOpen> { ... }
}

impl CircuitBreaker<HalfOpen> {
    fn close(self) -> CircuitBreaker<Closed> { ... }
    fn trip(self) -> CircuitBreaker<Open> { ... }
}
```

## Trait Objects Under the Hood

```
dyn Trait = (data_ptr, vtable_ptr)

vtable layout:
┌────────────────────────┐
│ drop_in_place pointer  │
│ size of concrete type  │
│ alignment              │
│ method_1 pointer       │
│ method_2 pointer       │
│ ...                    │
└────────────────────────┘
```

```rust
// Fat pointer: 2 × pointer width (16 bytes on 64-bit)
assert_eq!(std::mem::size_of::<&dyn Display>(), 16);
assert_eq!(std::mem::size_of::<&u32>(), 8);
```

## Downcasting

```rust
use std::any::Any;

trait Plugin: Any {
    fn name(&self) -> &str;
    fn as_any(&self) -> &dyn Any;
}

impl dyn Plugin {
    fn downcast<T: 'static>(&self) -> Option<&T> {
        self.as_any().downcast_ref::<T>()
    }
}
```

## Variance Deep Dive

How generic parameters relate to subtyping:

```rust
// Covariant: if 'a: 'b (a outlives b), then &'a T is subtype of &'b T
fn covariant<'a, 'b>(long: &'a str) -> &'b str
where 'a: 'b
{
    long  // OK: longer lifetime coerces to shorter
}

// Invariant: &'a mut T is invariant in T
// Cannot coerce &mut Vec<&'static str> to &mut Vec<&'a str>
// (because you could push a short-lived reference through the wider type)
```

## Impl Blocks and Coherence

```rust
// Blanket impl: implements for ALL types matching bound
impl<T: Display> ToString for T { ... }

// Specialized impl (not yet stable; requires specialization)
// impl ToString for String { ... }  // would override blanket

// Workaround: use a helper trait with different priority
trait ToStringFast { fn to_string_fast(&self) -> String; }
impl<T: Display> ToStringFast for T { ... }
impl ToStringFast for String {
    fn to_string_fast(&self) -> String { self.clone() }
}
```

## Compile-Time Assertions

```rust
// Assert a type implements a trait
const _: () = {
    fn assert_send<T: Send>() {}
    assert_send::<MyType>();
};

// Assert size
const _: () = assert!(std::mem::size_of::<MyStruct>() <= 64);
```
