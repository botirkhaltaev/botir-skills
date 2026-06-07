# Macro Patterns & Building Blocks

## TT Muncher

Process tokens one-by-one via recursion:

```rust
macro_rules! replace_expr {
    // Base case: no tokens left
    ($_t:tt $sub:expr) => { $sub };
}

macro_rules! count_tts {
    () => { 0 };
    ($head:tt $($tail:tt)*) => { 1 + count_tts!($($tail)*) };
}
```

Useful for: counting, transforming token streams, implementing DSLs.

## Push-Down Accumulation

Build up output in an accumulator, emit at the end:

```rust
macro_rules! reverse {
    // Entry: start with empty accumulator
    ($($all:tt)*) => { reverse!(@acc [] $($all)*) };
    // Done: emit accumulated
    (@acc [$($acc:tt)*]) => { ($($acc)*) };
    // Recurse: move head to front of accumulator
    (@acc [$($acc:tt)*] $head:tt $($tail:tt)*) => {
        reverse!(@acc [$head $($acc)*] $($tail)*)
    };
}
```

## Callback Pattern

One macro invokes another with its result:

```rust
macro_rules! call_with_count {
    ($callback:ident, $($args:tt)*) => {
        $callback!(count_tts!($($args)*))
    };
}
```

## Enum Dispatch

Generate match arms for all variants:

```rust
macro_rules! dispatch {
    ($self:expr, $method:ident $(, $arg:expr)*; $($variant:ident),+) => {
        match $self {
            $(Self::$variant(inner) => inner.$method($($arg),*),)+
        }
    };
}
```

## Impl Blocks for Multiple Types

```rust
macro_rules! impl_from_int {
    ($($t:ty),+) => {
        $(
            impl From<$t> for Value {
                fn from(v: $t) -> Self { Value::Int(v as i64) }
            }
        )+
    };
}

impl_from_int!(i8, i16, i32, i64, u8, u16, u32);
```

## Counted Repetition

Count items without allocating:

```rust
macro_rules! count_items {
    ($($item:tt),*) => {
        <[()]>::len(&[$(replace_expr!($item ())),*])
    };
}
```

## Stringification

```rust
macro_rules! stringify_call {
    ($fn:ident($($arg:expr),*)) => {
        println!("calling {}({})",
            stringify!($fn),
            stringify!($($arg),*)
        );
        $fn($($arg),*)
    };
}
```

## Compile-Time Validation

```rust
macro_rules! assert_fields {
    ($t:ty, $($field:ident),+) => {
        #[allow(dead_code)]
        fn _assert_fields(v: &$t) {
            $(let _ = &v.$field;)+
        }
    };
}

assert_fields!(MyStruct, name, age);  // compile error if fields don't exist
```

## DSL Example: Builder

```rust
macro_rules! builder {
    ($name:ident { $($field:ident : $ty:ty),* $(,)? }) => {
        pub struct $name { $($field: $ty),* }

        impl $name {
            pub fn builder() -> paste::paste!{[< $name Builder >]} {
                paste::paste!{[< $name Builder >]}::default()
            }
        }

        #[derive(Default)]
        pub struct paste::paste!{[< $name Builder >]} {
            $($field: Option<$ty>),*
        }

        impl paste::paste!{[< $name Builder >]} {
            $(
                pub fn $field(mut self, val: $ty) -> Self {
                    self.$field = Some(val);
                    self
                }
            )*

            pub fn build(self) -> Result<$name, &'static str> {
                Ok($name {
                    $($field: self.$field.ok_or(concat!(
                        "missing field: ", stringify!($field)
                    ))?),*
                })
            }
        }
    };
}
```

## Anti-Patterns to Avoid

1. **Overusing macros** — prefer generics + traits when possible
2. **Macro spaghetti** — deeply nested recursive macros are hard to debug
3. **Breaking IDE support** — complex macros defeat rust-analyzer
4. **Ignoring `cargo expand`** — always verify expansion
