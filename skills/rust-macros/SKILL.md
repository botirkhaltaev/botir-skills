---
name: rust-macros
description: Write declarative and procedural Rust macros. Use when creating macro_rules! macros, derive macros, attribute macros, or function-like proc macros. Covers metavariables, repetitions, TT munching, syn/quote/proc-macro2.
---

# Rust Macros

Comprehensive guide based on The Little Book of Rust Macros, the Rust Reference, and procedural macro workshop materials.

## When to Use This Skill

- Writing `macro_rules!` declarative macros
- Creating derive macros (`#[derive(MyTrait)]`)
- Creating attribute macros (`#[my_attr]`)
- Creating function-like proc macros (`my_macro!(...)`)
- Debugging macro expansion issues
- Understanding hygiene, metavariables, and fragment specifiers

## Declarative Macros (macro_rules!)

### Basic Syntax

```rust
macro_rules! say_hello {
    () => { println!("Hello!") };
    ($name:expr) => { println!("Hello, {}!", $name) };
}

say_hello!();           // "Hello!"
say_hello!("world");    // "Hello, world!"
```

### Fragment Specifiers

| Specifier | Matches | Example |
|-----------|---------|---------|
| `expr` | Expression | `2 + 2`, `foo()` |
| `ty` | Type | `i32`, `Vec<String>` |
| `ident` | Identifier | `foo`, `MyStruct` |
| `pat` | Pattern | `Some(x)`, `_` |
| `path` | Path | `std::io::Error` |
| `tt` | Token tree | any single token or `(...)` `[...]` `{...}` group |
| `stmt` | Statement | `let x = 1` |
| `block` | Block | `{ ... }` |
| `item` | Item | `fn foo() {}` |
| `literal` | Literal | `42`, `"hi"` |
| `lifetime` | Lifetime | `'a`, `'static` |
| `vis` | Visibility | `pub`, `pub(crate)` |
| `meta` | Attribute content | `derive(Debug)` |

### Repetitions

```rust
macro_rules! vec_of {
    ($($element:expr),* $(,)?) => {
        {
            let mut v = Vec::new();
            $(v.push($element);)*
            v
        }
    };
}

let v = vec_of![1, 2, 3];
```

Repetition operators:
- `*` — zero or more
- `+` — one or more
- `?` — zero or one

### Multiple Rules (Pattern Matching)

```rust
macro_rules! calculate {
    (add $a:expr, $b:expr) => { $a + $b };
    (mul $a:expr, $b:expr) => { $a * $b };
}
```

Rules are tried top-to-bottom; first match wins.

### TT Muncher Pattern

Process tokens one at a time recursively:

```rust
macro_rules! count {
    () => { 0usize };
    ($head:tt $($tail:tt)*) => { 1usize + count!($($tail)*) };
}

assert_eq!(count!(a b c d), 4);
```

### Internal Rules Pattern

Use `@` prefix for internal helper rules:

```rust
macro_rules! my_macro {
    // Public entry
    ($($input:tt)*) => { my_macro!(@internal [] $($input)*) };
    // Internal accumulator
    (@internal [$($acc:tt)*]) => { /* done */ };
    (@internal [$($acc:tt)*] $head:tt $($tail:tt)*) => {
        my_macro!(@internal [$($acc)* $head] $($tail)*)
    };
}
```

## Procedural Macros

### Setup

Proc macros live in their own crate:

```toml
# Cargo.toml
[lib]
proc-macro = true

[dependencies]
syn = { version = "2", features = ["full"] }
quote = "1"
proc-macro2 = "1"
```

### Derive Macro

```rust
use proc_macro::TokenStream;
use quote::quote;
use syn::{parse_macro_input, DeriveInput};

#[proc_macro_derive(MyTrait)]
pub fn derive_my_trait(input: TokenStream) -> TokenStream {
    let input = parse_macro_input!(input as DeriveInput);
    let name = input.ident;

    let expanded = quote! {
        impl MyTrait for #name {
            fn describe(&self) -> &'static str {
                stringify!(#name)
            }
        }
    };

    TokenStream::from(expanded)
}
```

### Attribute Macro

```rust
#[proc_macro_attribute]
pub fn log_calls(attr: TokenStream, item: TokenStream) -> TokenStream {
    let input = parse_macro_input!(item as syn::ItemFn);
    let name = &input.sig.ident;
    let block = &input.block;
    let sig = &input.sig;

    let expanded = quote! {
        #sig {
            println!("calling {}", stringify!(#name));
            #block
        }
    };

    TokenStream::from(expanded)
}
```

### Function-like Proc Macro

```rust
#[proc_macro]
pub fn sql(input: TokenStream) -> TokenStream {
    let query = input.to_string();
    // Parse and validate SQL at compile time
    let expanded = quote! { /* ... */ };
    TokenStream::from(expanded)
}
```

## Debugging Macros

```bash
# Expand all macros
cargo expand

# Expand a specific item
cargo expand my_module::MyStruct

# Install cargo-expand
cargo install cargo-expand
```

## Reference Map

- `references/declarative-macros.md` — macro_rules! patterns, hygiene, scoping
- `references/procedural-macros.md` — syn/quote, derive/attribute/function-like
- `references/macro-patterns.md` — TT muncher, push-down, callbacks, counting

## Key References

- [The Little Book of Rust Macros](https://veykril.github.io/tlborm/)
- [Rust Reference: Macros](https://doc.rust-lang.org/reference/macros.html)
- [proc-macro-workshop](https://github.com/dtolnay/proc-macro-workshop)
