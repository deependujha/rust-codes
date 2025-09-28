# Conditional Compilation & Attributes (`cfg` & `cfg_attr`)

Rust’s `cfg` system allows conditional compilation based on platform, features, or custom flags. It works at compile time, so irrelevant code is never even built.

## Usage Forms

### Attributes

```rust
#[cfg(target_os = "linux")]
fn special_linux_function() { /* ... */ }

#[cfg(feature = "serde")]
mod with_serde;
```

### Inline Expressions (`cfg! macro`)

* `cfg!` expands to a `bool` at **runtime** (evaluated at compile-time, but value is inlined).

```rust
if cfg!(target_os = "windows") {
    println!("Running on Windows!");
}
```

### Conditional Attributes (`cfg_attr`)

- If this `cfg condition is true`, `apply this other attribute`. If not, ignore it.

```rust
#[cfg_attr(feature = "serde", derive(serde::Serialize, serde::Deserialize))]
struct Point {
    x: i32,
    y: i32,
}
```


## Common Predicates

* `target_os = "linux" | "windows" | "macos" | ...`
* `target_arch = "x86" | "x86_64" | "arm" | ...`
* `target_pointer_width = "32" | "64"`
* `debug_assertions` → `true` in debug builds
* `feature = "name"` → enabled Cargo feature (specify in `Cargo.toml`)

!!! info "What is `debug_assertions`?"
    This is enabled by default in debug builds and disabled in release builds. It allows you to include code that should only run in debug mode, such as additional checks or logging.

    ```rust
    fn main(){
        if cfg!(debug_assertions){
            println!("debug mode");
        } else {
            println!("release mode");
        }
    }
    ```

    - run in debug & release mode:

    ```bash
    cargo run # default is debug
    cargo run --release # release mode
    ```

## Compound Conditions

```rust
#[cfg(all(unix, feature = "logging"))]
fn log_unix() {}

#[cfg(any(target_os = "linux", target_os = "macos"))]
fn posix_only() {}

#[cfg(not(feature = "gui"))]
fn headless() {}
```

## Cargo Integration

### Features are defined in `Cargo.toml`:

```toml
[features]
default = []
serde = ["serde_crate"]
extra = []
```

* A feature is a **mapping**: `name = [ list of other features or optional deps ]`.
* `default` (special) → automatically enabled unless `--no-default-features`.
* Entries in the list can be:
    * another feature name (enables that feature),
    * `dep:NAME` to pull in an optional dependency.

### Example

```toml
[dependencies]
dep1 = { version = "1", optional = true }
dep2 = { version = "1", optional = true }
dep3 = { version = "1", optional = true }

[features]
default = []
feat1 = ["dep:dep1", "dep:dep2/featureA"]
feat2 = ["feat1", "dep:dep3"]
```

* All features are disabled by default. To make them enabled by default, add them to `default`:

```toml
[features]
default = ["feat1"]
feat1 = ["dep:dep1", "dep:dep2/featureA"]
```

* `cargo run --features feat2` → enables `feat2`, which pulls in `feat1`, `dep1`, `dep2`, and `dep3`.
* Optional deps (`optional = true`) are **not included** unless gated behind a feature that enables them.
* To enable specific feature of dependency: `dep_name/feature_name`.
* Use `?/` to make a feature optional: `dep_name?/feature_name`. It means, "if `dep_name` is **already included (by default or some other feature)**, then only enable `feature_name`".

??? Warning "see example from tokio project"
    ```toml
    [package]
    name = "tokio-stream"
    # When releasing to crates.io:
    # - Remove path dependencies
    # - Update CHANGELOG.md.
    # - Create "tokio-stream-0.1.x" git tag.
    version = "0.1.17"
    edition = "2021"
    rust-version = "1.70"
    authors = ["Tokio Contributors <team@tokio.rs>"]
    license = "MIT"
    repository = "https://github.com/tokio-rs/tokio"
    homepage = "https://tokio.rs"
    description = """
    Utilities to work with `Stream` and `tokio`.
    """
    categories = ["asynchronous"]

    [features]
    default = ["time"]

    full = [
        "time",
        "net",
        "io-util",
        "fs",
        "sync",
        "signal"
    ]

    time = ["tokio/time"]
    net = ["tokio/net"]
    io-util = ["tokio/io-util"]
    fs = ["tokio/fs"]
    sync = ["tokio/sync", "tokio-util"]
    signal = ["tokio/signal"]

    [dependencies]
    futures-core = { version = "0.3.0" }
    pin-project-lite = "0.2.11"
    tokio = { version = "1.15.0", path = "../tokio", features = ["sync"] }
    tokio-util = { version = "0.7.0", path = "../tokio-util", optional = true }

    [dev-dependencies]
    tokio = { version = "1.2.0", path = "../tokio", features = ["full", "test-util"] }
    async-stream = "0.3"
    parking_lot = "0.12.0"
    tokio-test = { version = "0.4", path = "../tokio-test" }
    futures = { version = "0.3", default-features = false }

    [package.metadata.docs.rs]
    all-features = true
    rustdoc-args = ["--cfg", "docsrs"]
    # Issue #3770
    #
    # This should allow `docsrs` to be read across projects, so that `tokio-stream`
    # can pick up stubbed types exported by `tokio`.
    rustc-args = ["--cfg", "docsrs"]

    [lints]
    workspace = true
    ```

    - `tokio/sync` feature is included by default, bcoz no `optional = true`. But, other features like `tokio/fs` is enabled only when using `--features fs`.

### Using:

```rust
#[cfg(feature = "extra")]
fn fancy() {}
```

* Enable with:

```sh
cargo build --features serde
cargo build --features "serde extra"
cargo test --no-default-features --features serde
```

## Dependencies per feature

```toml
[dependencies]
serde = { version = "1", optional = true }
regex = { version = "1" }

[features]
default = ["regex"]
serde_support = ["serde"]
```

* `optional = true` → only included if its feature is enabled.

---

## Tests with `cfg`

```rust
#[cfg(test)]
mod tests {
    #[test]
    fn basic() {}
}

#[cfg(all(test, feature = "serde"))]
mod serde_tests {
    #[test]
    fn test_with_serde() {}
}
```

## Notes

* `#[cfg]` **removes code entirely** at compile-time.
* `cfg!` only provides a `bool`; it does not remove code.
* `cfg_attr` allows for conditional attributes.
* Use attributes for conditional modules, imports, or functions.
* Use `cfg!` for branching in code paths.
