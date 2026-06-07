# Procedural Macros

## Architecture

Proc macros run at compile time as compiler plugins. They take `TokenStream` → `TokenStream`.

```
┌─────────────┐     ┌──────────────┐     ┌─────────────┐
│ Source Code │ ──→ │ Proc Macro   │ ──→ │ Expanded    │
│ (tokens)    │     │ (Rust code)  │     │ Source Code │
└─────────────┘     └──────────────┘     └─────────────┘
```

Three kinds:
1. **Derive** — `#[derive(MyMacro)]` — appends impl blocks
2. **Attribute** — `#[my_attr]` — replaces the annotated item
3. **Function-like** — `my_macro!(...)` — replaces the invocation

## Key Libraries

### syn — Parse tokens into AST

```rust
use syn::{parse_macro_input, DeriveInput, Data, Fields};

let input = parse_macro_input!(tokens as DeriveInput);
let name = &input.ident;
let generics = &input.generics;

match &input.data {
    Data::Struct(data) => match &data.fields {
        Fields::Named(fields) => { /* struct { field: Type } */ }
        Fields::Unnamed(fields) => { /* struct(Type) */ }
        Fields::Unit => { /* struct Unit; */ }
    }
    Data::Enum(data) => { /* enum variants */ }
    Data::Union(data) => { /* union */ }
}
```

### quote — Generate tokens from Rust-like syntax

```rust
use quote::quote;

let expanded = quote! {
    impl #name {
        pub fn new() -> Self {
            Self { #(#field_inits),* }
        }
    }
};
```

Interpolation: `#var` inserts a variable, `#(#iter)*` repeats for iterables.

### proc-macro2 — Testable token types

`proc_macro` types can't be used outside the compiler. `proc-macro2` provides equivalent types that work in unit tests.

## Derive Macro With Helper Attributes

```rust
#[proc_macro_derive(Builder, attributes(builder))]
pub fn derive_builder(input: TokenStream) -> TokenStream {
    // Can now parse #[builder(default)] on fields
    ...
}
```

Usage:
```rust
#[derive(Builder)]
struct Config {
    #[builder(default)]
    port: u16,
    host: String,
}
```

## Parsing Custom Syntax

```rust
use syn::parse::{Parse, ParseStream};
use syn::{Ident, Token, LitStr};

struct Route {
    method: Ident,
    path: LitStr,
}

impl Parse for Route {
    fn parse(input: ParseStream) -> syn::Result<Self> {
        let method: Ident = input.parse()?;
        input.parse::<Token![,]>()?;
        let path: LitStr = input.parse()?;
        Ok(Route { method, path })
    }
}
```

## Error Reporting

```rust
use syn::Error;

// Point error at specific span for good diagnostics
let err = Error::new_spanned(&field.ident, "field must be pub");
return err.to_compile_error().into();

// Multiple errors
let mut errors = Vec::new();
errors.push(Error::new(span, "first problem"));
errors.push(Error::new(span2, "second problem"));
let combined = errors.into_iter().reduce(|mut a, b| { a.combine(b); a });
```

## Testing Proc Macros

Use `trybuild` for compile-fail tests:

```rust
#[test]
fn ui_tests() {
    let t = trybuild::TestCases::new();
    t.pass("tests/01-parse.rs");
    t.compile_fail("tests/02-missing-field.rs");
}
```

## Performance Tips

- Avoid parsing the full AST if you only need identifiers
- Use `syn::parse::Nothing` for attributes you want to acknowledge but ignore
- Cache parsed results across multiple derive invocations in the same file
- Feature-gate `syn` features: `syn = { version = "2", features = ["derive"] }` (not "full")
