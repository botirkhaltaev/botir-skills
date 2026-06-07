# Testing & Distribution

## Integration Testing with assert_cmd

```toml
[dev-dependencies]
assert_cmd = "2"
predicates = "3"
tempfile = "3"
```

```rust
use assert_cmd::Command;
use predicates::prelude::*;
use tempfile::TempDir;

#[test]
fn test_version() {
    Command::cargo_bin("mycli").unwrap()
        .arg("--version")
        .assert()
        .success()
        .stdout(predicate::str::starts_with("mycli "));
}

#[test]
fn test_file_processing() {
    let dir = TempDir::new().unwrap();
    let input = dir.path().join("input.txt");
    std::fs::write(&input, "hello\nworld\n").unwrap();

    Command::cargo_bin("mycli").unwrap()
        .args(["process", input.to_str().unwrap()])
        .assert()
        .success()
        .stdout(predicate::str::contains("2 lines processed"));
}

#[test]
fn test_stdin_input() {
    Command::cargo_bin("mycli").unwrap()
        .arg("process")
        .arg("--stdin")
        .write_stdin("line1\nline2\n")
        .assert()
        .success();
}

#[test]
fn test_error_on_missing_file() {
    Command::cargo_bin("mycli").unwrap()
        .args(["process", "/nonexistent/file.txt"])
        .assert()
        .failure()
        .stderr(predicate::str::contains("No such file"));
}
```

## Snapshot Testing with insta

```rust
use insta::assert_snapshot;

#[test]
fn test_help_output() {
    let output = Command::cargo_bin("mycli").unwrap()
        .arg("--help")
        .output().unwrap();
    assert_snapshot!(String::from_utf8_lossy(&output.stdout));
}
```

## Cross-Compilation

```bash
# Install targets
rustup target add x86_64-unknown-linux-musl
rustup target add x86_64-apple-darwin
rustup target add x86_64-pc-windows-msvc

# Build for Linux (static binary)
cargo build --release --target x86_64-unknown-linux-musl

# Use cross for easy cross-compilation
cargo install cross
cross build --release --target aarch64-unknown-linux-gnu
```

## Distribution

### GitHub Releases (with cargo-dist)

```toml
# Cargo.toml
[workspace.metadata.dist]
cargo-dist-version = "0.5.0"
installers = ["shell", "powershell", "homebrew"]
targets = [
    "x86_64-unknown-linux-gnu",
    "aarch64-unknown-linux-gnu",
    "x86_64-apple-darwin",
    "aarch64-apple-darwin",
    "x86_64-pc-windows-msvc",
]
```

### Homebrew

```bash
cargo install cargo-homebrew
cargo homebrew --create-formula
```

### crates.io

```bash
cargo publish
# Users install with: cargo install mycli
```

## Man Pages

```rust
use clap_mangen::Man;

fn generate_man_pages() {
    let cmd = Cli::command();
    let man = Man::new(cmd);
    let mut buffer = Vec::new();
    man.render(&mut buffer).unwrap();
    std::fs::write("mycli.1", buffer).unwrap();
}
```

## CI/CD Pattern

```yaml
# .github/workflows/release.yml
name: Release
on:
  push:
    tags: ['v*']

jobs:
  build:
    strategy:
      matrix:
        include:
          - os: ubuntu-latest
            target: x86_64-unknown-linux-musl
          - os: macos-latest
            target: x86_64-apple-darwin
          - os: windows-latest
            target: x86_64-pc-windows-msvc
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v4
      - uses: dtolnay/rust-toolchain@stable
        with:
          targets: ${{ matrix.target }}
      - run: cargo build --release --target ${{ matrix.target }}
      - uses: actions/upload-artifact@v4
        with:
          name: binary-${{ matrix.target }}
          path: target/${{ matrix.target }}/release/mycli*
```

## Completions in CI

Generate completions during build:

```rust
// build.rs
use clap::CommandFactory;
use clap_complete::{generate_to, Shell};
include!("src/cli.rs");

fn main() {
    let outdir = std::env::var_os("OUT_DIR").unwrap();
    let mut cmd = Cli::command();
    for shell in [Shell::Bash, Shell::Zsh, Shell::Fish] {
        generate_to(shell, &mut cmd, "mycli", &outdir).unwrap();
    }
}
```
