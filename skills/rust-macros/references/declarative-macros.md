# Declarative Macros (macro_rules!)

## Hygiene

Macro hygiene prevents accidental name collisions:

```rust
macro_rules! using_x {
    ($e:expr) => {
        {
            let x = 42;   // This 'x' is in a different scope
            $e             // If $e references 'x', it's the caller's x
        }
    };
}

let x = 10;
assert_eq!(using_x!(x + 1), 11);  // uses caller's x=10, not macro's x=42
```

Identifiers created inside a macro are in the macro's syntax context and don't leak out.

## Scoping and Import

Macros are available after their definition point in the source:

```rust
// Must define before use in same file
macro_rules! helper { () => {} }
helper!();
```

### Cross-module usage

```rust
// In lib.rs or a module
#[macro_export]  // makes macro available at crate root
macro_rules! public_macro { ... }

// Usage from another crate
use my_crate::public_macro;
```

Within the same crate, use `#[macro_use]` on a module or `#[macro_export]`:

```rust
#[macro_use]
mod macros;  // makes macros defined in macros.rs available below
```

## Advanced Matching

### Matching on types and paths

```rust
macro_rules! impl_display {
    ($t:ty, $fmt:expr) => {
        impl std::fmt::Display for $t {
            fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
                write!(f, $fmt, self)
            }
        }
    };
}
```

### Matching specific tokens

```rust
macro_rules! command {
    (get $url:expr) => { Request::get($url) };
    (post $url:expr, $body:expr) => { Request::post($url).body($body) };
}
```

### Optional trailing comma

```rust
macro_rules! my_vec {
    ($($e:expr),* $(,)?) => { ... };
    //                ^^^ optional trailing comma
}
```

## Fragment Follow-Set Rules

Each fragment specifier has restrictions on what tokens can follow it:

| Fragment | Can be followed by |
|----------|-------------------|
| `expr`, `stmt` | `=>`, `,`, `;` |
| `ty`, `path` | `=>`, `,`, `;`, `=`, `\|`, `>`, `>>`, `[`, `{`, `as`, `where` |
| `pat` | `=>`, `,`, `=`, `\|`, `if`, `in` |
| `ident`, `lifetime` | anything |
| `tt`, `item`, `block`, `literal`, `meta` | anything |

## Recursive Macros

```rust
macro_rules! nested_tuple {
    ($first:expr) => { ($first,) };
    ($first:expr, $($rest:expr),+) => {
        ($first, nested_tuple!($($rest),+))
    };
}
// nested_tuple!(1, 2, 3) → (1, (2, (3,)))
```

## Metavariable Expressions (Nightly)

```rust
macro_rules! count_idents {
    ($($id:ident),*) => { ${count($id)} };
}
```

Available: `${count(…)}`, `${index()}`, `${length()}`, `${ignore(…)}`.

## Common Pitfalls

1. **Expression statements need semicolons**: `$($e:expr);*` not `$($e:expr)*`
2. **Eager vs lazy expansion**: macro_rules! is not eager — inner macros expand after outer
3. **Type position requires `ty`**: using `expr` where a type is needed causes cryptic errors
4. **Macro ordering matters**: macros must be defined before use in the same file
