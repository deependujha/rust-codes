# Clap Notes

## 1. Introduction to Clap

[Clap](https://docs.rs/clap/) is a Rust library for parsing command-line arguments. It provides an easy-to-use API for defining arguments, options, flags, and subcommands.

## 2. Adding Clap to Your Project

Add the following to `Cargo.toml`:

```toml
[dependencies]
clap = { version = "4", features = ["derive"] }
```

??? Warning "Why `features = ["derive"]` in `clap = { version = "4", features = ["derive"] }`?"

    By default, `clap` doesn’t include support for the **`#[derive(Parser)]` macro** unless you explicitly enable the `"derive"` feature.

    ---

    ### 🔍 Without `features = ["derive"]`

    You’d have to build your CLI manually using the builder API:

    ```rust
    use clap::{Arg, Command};

    let matches = Command::new("myapp")
        .arg(Arg::new("verbose").short('v').long("verbose"))
        .get_matches();
    ```

    ---

    ### ✅ With `features = ["derive"]`

    You unlock the `#[derive(Parser)]` macro and everything it needs (like `Subcommand`, `Args`, etc.):

    ```rust
    use clap::Parser;

    #[derive(Parser)]
    struct Cli {
        #[arg(short, long)]
        verbose: bool,
    }
    ```

    This declarative approach is cleaner, especially for complex CLIs.

    ---

    ### TL;DR

    * `clap` is modular to reduce compile time and binary size.
    * The `"derive"` feature enables procedural macros like `#[derive(Parser)]`.
    * Always include it if you’re using `derive`-style command line parsing.


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

## 8. Help and Version Auto-Generation

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

---

## 9. `Nested Subcommands`

> You can nest subcommands inside other subcommands using enums.

### ✅ Example: `user update bio`, `user update dp`

```rust
use clap::{Parser, Subcommand};

#[derive(Parser)]
#[command(name = "app")]
struct Cli {
    #[command(subcommand)]
    command: Commands,
}

#[derive(Subcommand)]
enum Commands {
    User(UserCommand),
    // Other top-level commands
}

#[derive(Subcommand)]
enum UserCommand {
    Update(UpdateCommand),
    Show,
}

#[derive(Subcommand)]
enum UpdateCommand {
    Bio {
        #[arg()]
        text: String,
    },
    Dp {
        #[arg()]
        path: String,
    },
}
```

### 🧪 Example CLI Usage

```sh
app user update bio "cool engineer at tau"
app user update dp ~/me.png
app user show
```

### 🧠 Pattern Matching

```rust
fn main() {
    let cli = Cli::parse();

    match &cli.command {
        Commands::User(user_cmd) => match user_cmd {
            UserCommand::Update(update_cmd) => match update_cmd {
                UpdateCommand::Bio { text } => println!("Updating bio: {}", text),
                UpdateCommand::Dp { path } => println!("Updating dp: {}", path),
            },
            UserCommand::Show => println!("Showing user info"),
        },
    }
}
```

---

### 📝 Notes

* Each `Subcommand` enum can contain other enums marked with `#[derive(Subcommand)]`.
* You can go arbitrarily deep (though practically 2–3 levels is ideal).
* It’s good practice to keep logic modular with one module per command group if you grow large.

---

Absolutely. Let's enrich your `clap` notes with how to add **descriptions**, **long help**, **per-argument docs**, and **fine control over help output** — all in your usual markdown style.

---

## 10. Adding Description, Docs, and Help Text in `clap`

### 📦 At the CLI level (App-wide metadata)

```rust
use clap::Parser;

#[derive(Parser)]
#[command(
    name = "litracer",
    version = "0.1.0",
    author = "Deependu Jha",
    about = "Trace log visualizer for Lightning AI",
    long_about = "Converts structured logs into Chrome Trace format to analyze bottlenecks in distributed training."
)]
struct Cli {
    // ...
}
```

* `about` → short help shown in `--help`
* `long_about` → detailed explanation (multi-line allowed)

---

### 🔠 At the argument level

```rust
#[derive(Parser)]
struct Cli {
    /// Show more debugging output (shorthand: -v)
    #[arg(short, long, help = "Increase output verbosity")]
    verbose: bool,

    /// Optional input file to parse
    #[arg(short, long, help = "Specify a log file to trace", long_help = "Provide the path to the structured log file generated by Lightning AI components.")]
    input: Option<String>,
}
```

* `///` doc comments are parsed by default into help text
* `help = "..."` gives a custom help message
* `long_help = "..."` gives an extended message for `--help`

---

### 🧩 For Subcommands

```rust
#[derive(Subcommand)]
enum Commands {
    /// Parse and visualize a trace file
    Parse {
        #[arg(short, long, help = "Path to trace JSON")]
        file: String,
    },
    
    /// Compare two trace files
    Compare {
        #[arg(help = "Baseline trace")]
        base: String,

        #[arg(help = "Target trace")]
        target: String,
    },
}
```

Each subcommand and argument can have:

* `///` doc string (preferred for short messages)
* `.help =` or `.long_help =` (for richer formatting)

---

### ✍️ Pro Tips

* Use `\n\n` in `long_help` for paragraph breaks.
* Use Markdown in `long_about` if you display help in a custom UI.
* Keep short `help` concise for single-line display.

---

## 11. `Args` in `clap`

The `Args` derive macro in `clap` is the third sibling to `Parser` and `Subcommand`, and it's used to **modularize and reuse** argument definitions across multiple commands.

### 🧩 `#[derive(Args)]` — When and Why?

You use `Args` when you want to:

* Define a **reusable chunk** of arguments.
* **Compose** those args into multiple `Parser` or `Subcommand` structs.
* Keep your CLI definition **modular** and clean.

Think of it like a building block for your CLI's options and flags.

---

## ✅ Example: Shared Flags Between Subcommands

```rust
use clap::{Parser, Subcommand, Args};

#[derive(Parser)]
#[command(name = "litcli")]
struct Cli {
    #[command(subcommand)]
    command: Commands,
}

#[derive(Subcommand)]
enum Commands {
    /// Download a trace
    Download(DownloadArgs),

    /// Upload a trace
    Upload(UploadArgs),
}

/// Shared options for download/upload
#[derive(Args)]
struct CommonArgs {
    /// Enable verbose logging
    #[arg(short, long)]
    verbose: bool,

    /// Set the remote bucket
    #[arg(long)]
    bucket: String,
}

#[derive(Args)]
struct DownloadArgs {
    #[command(flatten)]
    common: CommonArgs,

    /// Name of the file to download
    #[arg(long)]
    file: String,
}

#[derive(Args)]
struct UploadArgs {
    #[command(flatten)]
    common: CommonArgs,

    /// Path to the file to upload
    #[arg(long)]
    path: String,
}
```

---

### 🧃 What's happening?

* `#[derive(Args)]`: Declares that this struct contains CLI arguments (but is *not* a full parser or subcommand).
* `#[command(flatten)]`: Merges the fields of the `CommonArgs` struct into the outer one. Useful for **composition**.
* Each command reuses the same chunk of flags (`--verbose`, `--bucket`) without redefining them.

---

## 🧠 TL;DR Cheatsheet

| Macro        | Purpose                            |
| ------------ | ---------------------------------- |
| `Parser`     | Top-level CLI parser               |
| `Subcommand` | Enum to define CLI subcommands     |
| `Args`       | Reusable set of arguments or flags |

---

## 12. Clap Completion


### `clap_complete`: Shell Completion for CLI Tools

`clap_complete` is a companion crate to [`clap`](https://docs.rs/clap) that enables shell completion generation (e.g. for `bash`, `zsh`, `fish`, `powershell`, `elvish`, etc.).

- You can also check this code for understanding: [clap_complete example](https://github.com/metafates/marky/blob/master/src/cli.rs)

---

#### 📦 Add Dependency

```toml
# Cargo.toml
[dependencies]
clap = { version = "4.5", features = ["derive"] }

[build-dependencies]
clap_complete = "4.5"
```

---

#### ⚙️ Basic Usage in `main.rs`

```rust
use clap::{Command, CommandFactory, Parser};
use clap_complete::aot::{Generator, Zsh, generate};
use std::io;

#[derive(Parser)]
struct Args {
    #[arg(short, long)]
    name: String,
}

fn print_completions<G: Generator>(generator: G, cmd: &mut Command) {
    generate(
        generator,
        cmd,
        cmd.get_name().to_string(),
        &mut io::stdout(),
    );
}

fn main() {
    let args = Args::parse();
    println!("Hello, {}!", args.name);

    print_completions(Zsh, &mut Args::command());
}
```

---

#### 📁 Optional: Write to File in Build Script (`build.rs`)

If you want to auto-generate completions on `cargo build`, use this:

```rust
use clap_complete::{generate_to, shells::Zsh};
use std::env;
use std::path::Path;

include!("src/cli.rs"); // wherever your build_cli() lives

fn main() {
    let outdir = env::var_os("OUT_DIR").unwrap();
    generate_to(
        Zsh,
        &mut Args::command(),
        "myapp",
        Path::new(&outdir),
    ).unwrap();
}
```

> 📝 Add generated path to install script or package instructions.

---

#### 💡 Notes

* You can use other shells too: `Bash`, `Zsh`, `Fish`, `PowerShell`, `Elvish`.
* The third argument in `generate` / `generate_to` must match the CLI binary name.
* If using subcommands, completions auto-include them!
