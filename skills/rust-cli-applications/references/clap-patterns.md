# clap Patterns

## Value Validation

```rust
#[derive(Parser)]
struct Cli {
    /// Port number (1-65535)
    #[arg(short, long, value_parser = clap::value_parser!(u16).range(1..))]
    port: u16,

    /// Output file (must have .json extension)
    #[arg(short, long, value_parser = validate_json_path)]
    output: PathBuf,
}

fn validate_json_path(s: &str) -> Result<PathBuf, String> {
    let path = PathBuf::from(s);
    if path.extension().map_or(false, |e| e == "json") {
        Ok(path)
    } else {
        Err("file must have .json extension".into())
    }
}
```

## Multiple Values

```rust
#[derive(Parser)]
struct Cli {
    /// Input files
    #[arg(required = true)]
    files: Vec<PathBuf>,

    /// Key-value pairs
    #[arg(short = 'D', value_parser = parse_key_val)]
    defines: Vec<(String, String)>,
}

fn parse_key_val(s: &str) -> Result<(String, String), String> {
    let (key, val) = s.split_once('=').ok_or("expected KEY=VALUE")?;
    Ok((key.to_string(), val.to_string()))
}
```

## Enum Arguments

```rust
#[derive(Clone, clap::ValueEnum)]
enum Format {
    Json,
    Yaml,
    Toml,
    #[value(alias = "txt")]
    Plain,
}

#[derive(Parser)]
struct Cli {
    #[arg(short, long, value_enum, default_value_t = Format::Json)]
    format: Format,
}
```

## Conditional Arguments

```rust
#[derive(Parser)]
struct Cli {
    #[command(subcommand)]
    command: Commands,
}

#[derive(Subcommand)]
enum Commands {
    /// Remote operations
    Remote {
        #[command(subcommand)]
        action: RemoteAction,
    },
}

#[derive(Subcommand)]
enum RemoteAction {
    Add { name: String, url: String },
    Remove { name: String },
    List,
}
```

## Shell Completions

```rust
use clap_complete::{generate, Shell};

#[derive(Parser)]
struct Cli {
    /// Generate shell completions
    #[arg(long, value_enum)]
    completions: Option<Shell>,
}

fn main() {
    let cli = Cli::parse();
    if let Some(shell) = cli.completions {
        generate(shell, &mut Cli::command(), "mycli", &mut std::io::stdout());
        return;
    }
}
```

```bash
# Generate and install
mycli --completions bash > /etc/bash_completion.d/mycli
mycli --completions zsh > ~/.zfunc/_mycli
mycli --completions fish > ~/.config/fish/completions/mycli.fish
```

## Environment Variable Fallback

```rust
#[derive(Parser)]
struct Cli {
    /// API token (or set MY_TOKEN env var)
    #[arg(long, env = "MY_TOKEN")]
    token: String,

    /// Database URL
    #[arg(long, env = "DATABASE_URL", default_value = "sqlite://data.db")]
    database_url: String,
}
```

## Conflicting & Required Groups

```rust
#[derive(Parser)]
#[command(group = clap::ArgGroup::new("input").required(true))]
struct Cli {
    /// Read from file
    #[arg(short, long, group = "input")]
    file: Option<PathBuf>,

    /// Read from stdin
    #[arg(long, group = "input")]
    stdin: bool,

    /// Inline value
    #[arg(short, long, group = "input")]
    value: Option<String>,
}
```

## Version & Long Version

```rust
#[derive(Parser)]
#[command(
    version,
    long_version = concat!(
        env!("CARGO_PKG_VERSION"),
        "\ncommit: ", env!("GIT_HASH"),
        "\nbuilt: ", env!("BUILD_DATE"),
    )
)]
struct Cli { ... }
```
