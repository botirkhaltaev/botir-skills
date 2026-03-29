# Builder Pattern

## Basic Builder

```rust
pub struct Config {
    host: String,
    port: u16,
    timeout: Duration,
}

impl Config {
    pub fn new() -> Self {
        Self {
            host: "localhost".to_string(),
            port: 8080,
            timeout: Duration::from_secs(30),
        }
    }
    
    pub fn host(mut self, host: &str) -> Self {
        self.host = host.to_string();
        self
    }
    
    pub fn port(mut self, port: u16) -> Self {
        self.port = port;
        self
    }
    
    pub fn timeout(mut self, timeout: Duration) -> Self {
        self.timeout = timeout;
        self
    }
}
```

## Usage

```rust
let config = Config::new()
    .host("example.com")
    .port(443)
    .timeout(Duration::from_secs(60));
```
