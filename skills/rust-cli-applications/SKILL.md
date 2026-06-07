---
name: rust-cli-applications
description: Build command-line applications in Rust with clap, signal handling, config files, logging, and testing. Use when writing CLI tools, parsing arguments, handling stdin/stdout, implementing interactive prompts, or testing CLI behavior.
---

# Rust CLI Applications

Based on Command Line Applications in Rust and community best practices.

## When to Use This Skill

- Parsing command-line arguments with clap
- Handling signals (Ctrl+C, SIGTERM)
- Reading/writing config files (TOML, JSON, YAML)
- Structured logging and colored output
- Testing CLI tools end-to-end
- Implementing progress bars and interactive prompts

## Project Setup

```toml
[package]
name = "my-cli"
version = "0.1.0"
edition = "2021"

[[bin]]
name = "mycli"
path = "src/main.rs"

[dependencies]
clap = { version = "4", features = ["derive"] }
anyhow = "1"
serde = { version = "1", features = ["derive"] }
toml = "0.8"
tracing = "0.1"
tracing-subscriber = { version = "0.3", features = ["env-filter"] }
```

## Argument Parsing with clap (Derive)

```rust
use clap::{Parser, Subcommand, Args};

#[derive(Parser)]
#[command(name = "mycli", version, about = "A useful CLI tool")]
struct Cli {
    /// Enable verbose output
    #[arg(short, long, global = true)]
    verbose: bool,

    /// Config file path
    #[arg(short, long, default_value = "config.toml")]
    config: PathBuf,

    #[command(subcommand)]
    command: Commands,
}

#[derive(Subcommand)]
enum Commands {
    /// Initialize a new project
    Init {
        /// Project name
        name: String,
        /// Template to use
        #[arg(short, long, default_value = "default")]
        template: String,
    },
    /// Run the build
    Build(BuildArgs),
    /// Deploy to production
    Deploy {
        /// Target environment
        #[arg(value_enum)]
        env: Environment,
    },
}

#[derive(Args)]
struct BuildArgs {
    /// Build in release mode
    #[arg(short, long)]
    release: bool,
    /// Number of parallel jobs
    #[arg(short, long, default_value_t = num_cpus::get())]
    jobs: usize,
}

#[derive(Clone, clap::ValueEnum)]
enum Environment { Dev, Staging, Prod }
```

## Main Function Pattern

```rust
fn main() -> anyhow::Result<()> {
    let cli = Cli::parse();
    setup_logging(cli.verbose);

    match cli.command {
        Commands::Init { name, template } => cmd_init(&name, &template)?,
        Commands::Build(args) => cmd_build(args)?,
        Commands::Deploy { env } => cmd_deploy(env)?,
    }

    Ok(())
}
```

## Signal Handling

```rust
use tokio::signal;

// Simple: handle Ctrl+C
ctrlc::set_handler(move || {
    eprintln!("\nInterrupted. Cleaning up...");
    cleanup();
    std::process::exit(130);
}).expect("Error setting Ctrl-C handler");

// With tokio
async fn run() -> anyhow::Result<()> {
    tokio::select! {
        result = do_work() => result,
        _ = signal::ctrl_c() => {
            eprintln!("Shutting down gracefully...");
            Ok(())
        }
    }
}
```

## Config Files

```rust
use serde::{Deserialize, Serialize};

#[derive(Debug, Deserialize, Serialize)]
struct Config {
    #[serde(default = "default_port")]
    port: u16,
    host: String,
    database: DatabaseConfig,
}

#[derive(Debug, Deserialize, Serialize)]
struct DatabaseConfig {
    url: String,
    max_connections: u32,
}

fn default_port() -> u16 { 8080 }

fn load_config(path: &Path) -> anyhow::Result<Config> {
    let content = std::fs::read_to_string(path)
        .context("reading config file")?;
    let config: Config = toml::from_str(&content)
        .context("parsing config file")?;
    Ok(config)
}
```

## Logging

```rust
use tracing::{info, warn, error, debug, instrument};
use tracing_subscriber::EnvFilter;

fn setup_logging(verbose: bool) {
    let filter = if verbose { "debug" } else { "info" };
    tracing_subscriber::fmt()
        .with_env_filter(EnvFilter::new(filter))
        .with_target(false)
        .init();
}

#[instrument(skip(config))]
fn process_file(path: &Path, config: &Config) -> anyhow::Result<()> {
    info!(?path, "processing file");
    // ...
    Ok(())
}
```

## Output & Formatting

```rust
// Colored output
use colored::Colorize;
println!("{} Configuration loaded", "✓".green());
eprintln!("{} File not found: {}", "error:".red().bold(), path.display());

// Progress bars
use indicatif::{ProgressBar, ProgressStyle};
let pb = ProgressBar::new(total);
pb.set_style(ProgressStyle::default_bar()
    .template("{bar:40} {pos}/{len} [{eta}]")?);
for item in items { pb.inc(1); process(item)?; }
pb.finish_with_message("done");

// Tables
use comfy_table::Table;
let mut table = Table::new();
table.set_header(vec!["Name", "Size", "Modified"]);
table.add_row(vec!["file.rs", "2.4 KB", "2024-01-15"]);
println!("{table}");
```

## Testing CLI Tools

```rust
use assert_cmd::Command;
use predicates::prelude::*;

#[test]
fn test_help() {
    Command::cargo_bin("mycli").unwrap()
        .arg("--help")
        .assert()
        .success()
        .stdout(predicate::str::contains("A useful CLI tool"));
}

#[test]
fn test_init() {
    let dir = tempfile::tempdir().unwrap();
    Command::cargo_bin("mycli").unwrap()
        .args(["init", "myproject"])
        .current_dir(&dir)
        .assert()
        .success();
    assert!(dir.path().join("myproject").exists());
}

#[test]
fn test_invalid_args() {
    Command::cargo_bin("mycli").unwrap()
        .arg("--nonexistent")
        .assert()
        .failure()
        .stderr(predicate::str::contains("unexpected argument"));
}
```

## Reference Map

- `references/clap-patterns.md` — argument types, validators, completions
- `references/io-patterns.md` — stdin/stdout, piping, file I/O, interactive prompts
- `references/testing-distribution.md` — assert_cmd, integration tests, cross-compilation

## Key References

- [Command Line Applications in Rust](https://rust-cli.github.io/book/)
- [clap docs](https://docs.rs/clap)
- [assert_cmd](https://docs.rs/assert_cmd)
