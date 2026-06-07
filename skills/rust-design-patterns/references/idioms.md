# Rust Idioms

## Concatenate Strings with format!

```rust
// Prefer format! for clarity
let full = format!("{} {}", first_name, last_name);

// Use push_str for building in a loop
let mut result = String::new();
for item in items {
    result.push_str(&item.name);
    result.push('\n');
}
```

## Use Iterators Over Index Loops

```rust
// Idiomatic
let sum: i32 = numbers.iter().filter(|&&n| n > 0).sum();

// Non-idiomatic (C-style)
let mut sum = 0;
for i in 0..numbers.len() {
    if numbers[i] > 0 { sum += numbers[i]; }
}
```

## Entry API for Maps

```rust
use std::collections::HashMap;

let mut scores: HashMap<&str, Vec<u32>> = HashMap::new();

// Insert-or-update without double lookup
scores.entry("Alice")
    .or_insert_with(Vec::new)
    .push(100);
```

## Option as Iterator

```rust
// Flatten Option into an iterator chain
let extra: Option<&str> = Some("bonus");
let all: Vec<&str> = items.iter()
    .chain(extra.iter())
    .copied()
    .collect();
```

## Temporary Bindings for Clarity

```rust
// Unclear
process(data.iter().filter(|x| x.valid).map(|x| x.id).collect());

// Clear
let valid_ids: Vec<_> = data.iter()
    .filter(|x| x.valid)
    .map(|x| x.id)
    .collect();
process(valid_ids);
```

## Use ? Early, Handle Late

```rust
fn load() -> Result<Config, Error> {
    let raw = fs::read_to_string("config.toml")?;  // propagate early
    let config: Config = toml::from_str(&raw)?;
    validate(&config)?;
    Ok(config)
}
```

## Impl From Instead of Custom Constructors

```rust
impl From<(f64, f64)> for Point {
    fn from((x, y): (f64, f64)) -> Self { Point { x, y } }
}

let p: Point = (1.0, 2.0).into();
```

## Accept impl Into<T> for Ergonomic APIs

```rust
pub fn connect(addr: impl Into<SocketAddr>) -> Connection { ... }

connect("127.0.0.1:8080".parse().unwrap());
connect(SocketAddr::from(([127, 0, 0, 1], 8080)));
```

## Cow for Flexible Ownership

```rust
use std::borrow::Cow;

fn process(input: Cow<'_, str>) -> Cow<'_, str> {
    if input.contains("bad") {
        Cow::Owned(input.replace("bad", "good"))
    } else {
        input  // no allocation if no modification needed
    }
}
```

## #[must_use] for Important Return Values

```rust
#[must_use = "this Result must be handled"]
pub fn save(&self) -> Result<(), SaveError> { ... }
```

## Sealed Traits (Prevent External Impls)

```rust
mod private { pub trait Sealed {} }

pub trait MyTrait: private::Sealed {
    fn method(&self);
}

impl private::Sealed for MyType {}
impl MyTrait for MyType { ... }
// External crates cannot implement MyTrait
```
