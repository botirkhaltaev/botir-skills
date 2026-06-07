# Anti-Patterns to Avoid

## Deref Polymorphism

Using `Deref` to simulate inheritance/subtyping:

```rust
// ANTI-PATTERN
struct Animal { name: String }
struct Dog { animal: Animal, breed: String }

impl Deref for Dog {
    type Target = Animal;
    fn deref(&self) -> &Animal { &self.animal }
}
// dog.name works via Deref — but this is NOT what Deref is for
```

**Why it's bad**: Deref is for smart pointers (`Box`, `Arc`, `Rc`), not OOP hierarchy. It confuses readers, breaks `DerefMut` expectations, and makes method resolution unpredictable.

**Instead**: Use composition with explicit delegation, or traits.

## Clone to Satisfy the Borrow Checker

```rust
// ANTI-PATTERN
let name = map.get("key").unwrap().clone();  // unnecessary clone
process(&name);

// BETTER: just borrow
if let Some(name) = map.get("key") {
    process(name);
}
```

Common legitimate uses of clone: when you genuinely need two independent owners, or when the borrow checker prevents a sound pattern (after confirming with `mem::take` etc.).

## God Object / Monster Struct

```rust
// ANTI-PATTERN
struct App {
    db: Database,
    cache: Cache,
    config: Config,
    logger: Logger,
    http_client: Client,
    // 20 more fields...
}

// BETTER: decompose into focused modules
struct App { db: DbPool, web: WebServer }
struct DbPool { ... }
struct WebServer { config: WebConfig, client: Client }
```

## Stringly Typed

```rust
// ANTI-PATTERN
fn process(action: &str, target: &str) {
    match action {
        "delete" => ...,
        "create" => ...,
        _ => panic!("unknown action"),  // runtime error
    }
}

// BETTER: use enums
enum Action { Delete, Create }
fn process(action: Action, target: Target) { ... }  // compile-time safety
```

## Denying the Borrow Checker (Excessive RefCell)

```rust
// ANTI-PATTERN: wrapping everything in RefCell to "shut up" the compiler
struct State {
    data: RefCell<Vec<Item>>,
    index: RefCell<HashMap<Id, usize>>,
}

// BETTER: restructure ownership so mutable access is straightforward
struct State { data: Vec<Item>, index: HashMap<Id, usize> }
// Pass &mut State to functions that modify it
```

RefCell is legitimate for shared-ownership graphs (trees, observers) but should not be the default escape hatch.

## Using unwrap() in Library Code

```rust
// ANTI-PATTERN in a library
pub fn parse(input: &str) -> Config {
    serde_json::from_str(input).unwrap()  // panics on invalid input
}

// BETTER: propagate the error
pub fn parse(input: &str) -> Result<Config, ParseError> {
    Ok(serde_json::from_str(input)?)
}
```

## Premature Abstraction

```rust
// ANTI-PATTERN: trait with one implementation
trait Storage { fn get(&self, key: &str) -> Option<String>; }
struct FileStorage;
impl Storage for FileStorage { ... }
// Only one impl exists; the trait just adds indirection

// BETTER: start concrete, extract trait when a second impl is needed
struct Storage { /* ... */ }
```

## Excessive Type Parameters

```rust
// ANTI-PATTERN
fn process<R: Read, W: Write, L: Logger, C: Cache>(r: R, w: W, l: L, c: C) { ... }

// BETTER: group related concerns into a context struct
struct Context<'a> {
    input: &'a mut dyn Read,
    output: &'a mut dyn Write,
    logger: &'a dyn Logger,
}
```

## Not Using the Type System

```rust
// ANTI-PATTERN: boolean arguments
fn connect(host: &str, use_tls: bool, verify_cert: bool) { ... }
connect("example.com", true, false);  // what do true/false mean?

// BETTER: enums or builder
enum Tls { Enabled, Disabled }
enum CertVerification { Verify, Skip }
fn connect(host: &str, tls: Tls, cert: CertVerification) { ... }
```
