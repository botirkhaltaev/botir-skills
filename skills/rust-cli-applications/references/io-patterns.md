# I/O Patterns for CLI Tools

## Reading stdin vs files

```rust
use std::io::{self, Read, BufRead, BufReader};
use std::fs::File;
use std::path::PathBuf;

fn get_reader(path: Option<&PathBuf>) -> anyhow::Result<Box<dyn BufRead>> {
    match path {
        Some(p) if p.to_str() != Some("-") => {
            Ok(Box::new(BufReader::new(File::open(p)?)))
        }
        _ => Ok(Box::new(BufReader::new(io::stdin().lock()))),
    }
}

// Usage: reads from file or stdin
fn process(input: Option<&PathBuf>) -> anyhow::Result<()> {
    let reader = get_reader(input)?;
    for line in reader.lines() {
        let line = line?;
        println!("{}", transform(&line));
    }
    Ok(())
}
```

## Writing to stdout/stderr

```rust
use std::io::{self, Write, BufWriter};

fn output_results(results: &[Record]) -> anyhow::Result<()> {
    // Buffered stdout for performance
    let stdout = io::stdout();
    let mut out = BufWriter::new(stdout.lock());

    for r in results {
        writeln!(out, "{}\t{}", r.name, r.value)?;
    }

    // Errors/progress always go to stderr
    eprintln!("Processed {} records", results.len());
    Ok(())
}
```

## Piping Detection

```rust
use std::io::IsTerminal;

fn main() {
    if io::stdout().is_terminal() {
        // Interactive: use colors, progress bars
        print_colored_output();
    } else {
        // Piped: plain text, no ANSI codes
        print_plain_output();
    }
}
```

## Atomic File Writes

Write to a temp file, then rename (prevents corruption):

```rust
use std::fs;
use tempfile::NamedTempFile;

fn write_atomically(path: &Path, content: &str) -> anyhow::Result<()> {
    let dir = path.parent().unwrap_or(Path::new("."));
    let mut tmp = NamedTempFile::new_in(dir)?;
    tmp.write_all(content.as_bytes())?;
    tmp.persist(path)?;
    Ok(())
}
```

## Interactive Prompts

```rust
use dialoguer::{Input, Confirm, Select, MultiSelect, Password};

let name: String = Input::new()
    .with_prompt("Project name")
    .default("my-project".into())
    .interact_text()?;

let proceed = Confirm::new()
    .with_prompt("Continue?")
    .default(true)
    .interact()?;

let choice = Select::new()
    .with_prompt("Pick a template")
    .items(&["minimal", "full", "library"])
    .default(0)
    .interact()?;

let password = Password::new()
    .with_prompt("Enter token")
    .interact()?;
```

## Streaming Large Files

```rust
use std::io::{BufReader, BufRead};

fn process_large_file(path: &Path) -> anyhow::Result<Stats> {
    let file = File::open(path)?;
    let reader = BufReader::with_capacity(64 * 1024, file);  // 64KB buffer

    let mut stats = Stats::default();
    for line in reader.lines() {
        let line = line?;
        stats.process_line(&line);
    }
    Ok(stats)
}
```

## JSON Lines (NDJSON)

```rust
use serde::Deserialize;
use std::io::BufRead;

#[derive(Deserialize)]
struct Record { id: u64, name: String }

fn read_jsonl(reader: impl BufRead) -> anyhow::Result<Vec<Record>> {
    reader.lines()
        .map(|line| {
            let line = line?;
            Ok(serde_json::from_str(&line)?)
        })
        .collect()
}
```

## Exit Codes

```rust
use std::process::ExitCode;

fn main() -> ExitCode {
    match run() {
        Ok(()) => ExitCode::SUCCESS,
        Err(e) => {
            eprintln!("error: {e:#}");
            ExitCode::from(1)
        }
    }
}

// Or with specific codes
const EXIT_CONFIG_ERROR: u8 = 2;
const EXIT_IO_ERROR: u8 = 3;
```

## Temp Directories

```rust
use tempfile::TempDir;

fn build_project() -> anyhow::Result<PathBuf> {
    let tmp = TempDir::new()?;
    let build_dir = tmp.path().join("build");
    fs::create_dir_all(&build_dir)?;
    // ... build into build_dir ...
    let output = build_dir.join("output.bin");
    let final_path = PathBuf::from("./dist/output.bin");
    fs::rename(&output, &final_path)?;
    // tmp auto-deleted on drop
    Ok(final_path)
}
```
