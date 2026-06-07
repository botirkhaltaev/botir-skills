# Lifetime Annotations

## When Annotations Are Required

The compiler requires explicit lifetimes when:
- A function returns a reference and has multiple reference inputs
- A struct holds a reference
- A trait method returns a reference not tied to `&self`

## Function Lifetimes

```rust
// Multiple inputs: compiler can't infer which input the output borrows from
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

// Output tied to only one input
fn first_sentence<'a>(text: &'a str, _sep: &str) -> &'a str {
    text.split('.').next().unwrap_or(text)
}
```

## Struct Lifetimes

```rust
struct Config<'a> {
    name: &'a str,
    values: &'a [u32],
}

impl<'a> Config<'a> {
    fn name(&self) -> &str { self.name }  // elision: output tied to &self
}
```

## Multiple Lifetimes

```rust
struct Parser<'input, 'alloc> {
    source: &'input str,
    arena: &'alloc Arena,
}
```

Use separate lifetimes when the references have genuinely different scopes.

## Higher-Ranked Trait Bounds (HRTB)

For callbacks that must work with **any** lifetime:

```rust
fn apply_to_ref<F>(f: F)
where
    F: for<'a> Fn(&'a str) -> &'a str,
{
    let owned = String::from("hello");
    let result = f(&owned);
    println!("{result}");
}
```

`for<'a>` means "for all possible lifetimes 'a" — the closure must work regardless of how long the reference lives.

## Lifetime Bounds on Generics

```rust
// T must not contain references shorter than 'a
fn store<'a, T: 'a>(container: &mut Vec<&'a T>, item: &'a T) {
    container.push(item);
}

// T: 'static means T has no non-static references
fn spawn<T: Send + 'static>(val: T) { ... }
```

## Subtyping and Coercion

A longer lifetime can be coerced to a shorter one:

```rust
fn example<'long, 'short>(s: &'long str)
where
    'long: 'short,  // 'long outlives 'short
{
    let _: &'short str = s;  // coercion OK
}
```

## Static Lifetime

- `'static` references: data embedded in the binary (string literals, `const`)
- `T: 'static`: T owns all its data (no borrows from local scopes)
- `&'static T`: reference valid for the entire program duration

```rust
let s: &'static str = "compile-time string";
let leaked: &'static str = Box::leak(String::from("runtime").into_boxed_str());
```

## Elision Rules (Recap)

| Input lifetimes | Output lifetime |
|----------------|----------------|
| One `&` param | Same lifetime |
| `&self` or `&mut self` among params | `self`'s lifetime |
| Multiple `&` params, no self | Must annotate explicitly |
