# Clap Notes

## 1. Introduction to Clap

[Clap](https://docs.rs/clap/) is a Rust library for parsing command-line arguments. It provides an easy-to-use API for defining arguments, options, flags, and subcommands.

## 2. Adding Clap to Your Project

Add the following to `Cargo.toml`:

```toml
[dependencies]
clap = { version = "4", features = ["derive"] }
```

## 3. Basic Usage

```rust
use clap::Parser;

#[derive(Parser)]
struct Args {
    #[arg(short, long)]
    name: String,
}

fn main() {
    let args = Args::parse();
    println!("Hello, {}!", args.name);
}
```

### Running

```sh
cargo run -- --name Alice
```

### Output

```txt
Hello, Alice!
```

## 4. Flags and Options

```rust
use clap::Parser;

#[derive(Parser)]
struct Args {
    #[arg(short, long)]
    verbose: bool,
}

fn main() {
    let args = Args::parse();
    if args.verbose {
        println!("Verbose mode enabled");
    }
}
```

### Running

```sh
cargo run -- --verbose
```

### Output

```txt
Verbose mode enabled
```

## 5. Default Values

```rust
use clap::Parser;

#[derive(Parser)]
struct Args {
    #[arg(short, long, default_value_t = 10)]
    count: u32,
}

fn main() {
    let args = Args::parse();
    println!("Count: {}", args.count);
}
```

## 6. Subcommands

```rust
use clap::{Parser, Subcommand};

#[derive(Parser)]
struct Cli {
    #[command(subcommand)]
    command: Commands,
}

#[derive(Subcommand)]
enum Commands {
    Add { value: i32 },
    Remove { value: i32 },
}

fn main() {
    let cli = Cli::parse();
    match cli.command {
        Commands::Add { value } => println!("Adding: {}", value),
        Commands::Remove { value } => println!("Removing: {}", value),
    }
}
```

### Running

```sh
cargo run -- add 42
cargo run -- remove 42
```

## 7. Validating Input

```rust
use clap::Parser;

#[derive(Parser)]
struct Args {
    #[arg(short, long, value_parser = clap::value_parser!(u32).range(1..=100))]
    percentage: u32,
}

fn main() {
    let args = Args::parse();
    println!("Percentage: {}", args.percentage);
}
```

## 8. Environment Variable Support

```rust
use clap::Parser;

#[derive(Parser)]
struct Args {
    #[arg(short, long, env = "USER_NAME")]
    name: String,
}

fn main() {
    let args = Args::parse();
    println!("Hello, {}!", args.name);
}
```

### Running

```sh
export USER_NAME=Bob
cargo run
```

### Output

```txt
Hello, Bob!
```

## 9. Help and Version Auto-Generation

Clap automatically generates help and version messages.

```rust
use clap::Parser;

#[derive(Parser)]
#[command(version = "1.0", about = "Example CLI tool")]
struct Args {}

fn main() {
    let _args = Args::parse();
}
```

### Running

```sh
cargo run -- --help
cargo run -- --version
```
