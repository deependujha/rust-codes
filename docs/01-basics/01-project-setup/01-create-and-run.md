# Rust Project Setup

## Install rust & update

```bash
rustc --version     # Check if Rust is installed & its version
rustup update       # Update Rust to the latest version
cargo --version     # Check if Cargo is installed & its version
```

---

## Create a new Rust Crate

!!! info ""
    - A crate is the smallest compilation unit in Rust.
    - It's either a binary (an executable) or a library (reusable code).
    - When you run cargo new, you create a package, which contains at least one crate.


!!! secondary ""
    ```
    my_project/
    ├── Cargo.toml
    ├── src/
    │   └── main.rs         # Default binary
    │   └── lib.rs         # Default library
    └── src/bin/
        ├── another_bin.rs  # Extra binary crate
        └── tool.rs         # Another binary crate
    ```

```bash
cargo new my_project --bin   # Create a new binary crate

cargo new my_library --lib   # Create a new library crate

# -----------------

cargo init my_project --bin  # Initialize in existing directory as binary crate

cargo init my_library --lib  # Initialize in existing directory as library crate
```

> if you don't specify `--bin` or `--lib`, Cargo will create a **`binary`** crate by default.

---

## Run the Rust Crate

```bash
cargo run # Run the current crate (main.rs)

cargo run --bin my_project # Run a specific binary crate
```

---

## Build the Rust Crate

```bash
cargo build # Build the current crate (main.rs)
cargo build --release # Build the current crate in release mode
cargo build --bin my_project # Build a specific binary crate
cargo build --bins      # Build all binary crates in the project
```

---

## Add & Remove external crates to project

- In existing projects, you can add dependencies by editing the `Cargo.toml` file.
- Alternatively, you can use the `cargo add` command to add crates directly from the command line.

```bash
cargo add <crate_name> # Add a crate to your project

cargo remove <crate_name> # Remove a crate from your project
```

---

## Install & Uninstall rust binary crates

```bash
cargo install <crate_name> # Install a binary crate globally

cargo uninstall <crate_name> # Uninstall a binary crate
```

---

## Install local rust code binary crate

```bash
cargo install --path . # Install the current crate as a binary
```

---

## 📦 Cargo.toml vs Cargo.lock

| Feature              | Cargo.toml                            | Cargo.lock                                 |
|----------------------|----------------------------------------|---------------------------------------------|
| Purpose              | Declare project & dependency rules     | Lock exact dependency versions              |
| Edited by            | You (manually)                         | Cargo (automatically)                       |
| Contains             | Dependency *requirements*              | Exact *resolved versions*                   |
| When updated         | When you change dependencies           | When dependencies are added/updated         |
| Committed to git     | ✅ Yes                                  | ✅ Yes (for binaries)<br>🚫 No (for libraries) |
| Role                 | Project setup & config                 | Reproducible builds across machines         |
