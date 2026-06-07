# Result & Option Deep Dive

## Option<T>

Represents a value that may or may not exist. NOT for error cases — for absence.

```rust
enum Option<T> {
    Some(T),
    None,
}
```

### Pattern Matching

```rust
match config.get("port") {
    Some(port) => start_server(*port),
    None => start_server(8080),
}

// if let for single-variant interest
if let Some(val) = optional_value {
    use_value(val);
}

// let-else for early return
let Some(val) = optional_value else {
    return Err(Error::missing("value"));
};
```

### Chaining

```rust
// map: Option<T> → Option<U>
let len: Option<usize> = name.map(|n| n.len());

// and_then: Option<T> → Option<U> (flatmap)
let parsed: Option<u16> = text.and_then(|t| t.parse().ok());

// filter: keep Some only if predicate holds
let even: Option<i32> = number.filter(|n| n % 2 == 0);

// zip: combine two Options
let pair: Option<(A, B)> = opt_a.zip(opt_b);

// flatten: Option<Option<T>> → Option<T>
let flat: Option<i32> = nested.flatten();
```

## Result<T, E>

Represents success or failure:

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

### The ? Operator

```rust
fn parse_file(path: &str) -> Result<Config, AppError> {
    let contents = std::fs::read_to_string(path)?;  // io::Error → AppError
    let config: Config = toml::from_str(&contents)?; // toml::Error → AppError
    Ok(config)
}
```

`?` does:
1. On `Ok(v)` — unwraps and continues
2. On `Err(e)` — calls `From::from(e)` and returns early

### Converting Between Option and Result

```rust
// Option → Result
let val: Result<i32, &str> = opt.ok_or("missing value");
let val: Result<i32, Error> = opt.ok_or_else(|| Error::new("missing"));

// Result → Option
let opt: Option<i32> = result.ok();   // discards error
let opt: Option<E> = result.err();    // discards success

// transpose
let x: Option<Result<i32, E>> = Some(Ok(5));
let y: Result<Option<i32>, E> = x.transpose();  // Ok(Some(5))
```

### Collecting Results

```rust
// Collect Vec<Result<T, E>> into Result<Vec<T>, E>
let results: Result<Vec<i32>, _> = strings.iter()
    .map(|s| s.parse::<i32>())
    .collect();

// Partition into successes and failures
let (oks, errs): (Vec<_>, Vec<_>) = results.into_iter().partition(Result::is_ok);
```

## Unwrap Family

| Method | On None/Err |
|--------|------------|
| `unwrap()` | panics with generic message |
| `expect("msg")` | panics with custom message |
| `unwrap_or(default)` | returns default |
| `unwrap_or_else(\|\| ...)` | returns computed default |
| `unwrap_or_default()` | returns `T::default()` |

**Rule**: Use `unwrap()`/`expect()` only when you can prove the value exists, or in tests.
