# Calling `C` code from `rust`

- File structure:

```md
├── build.rs
├── Cargo.lock
├── Cargo.toml
└── src
    ├── hello.c
    └── main.rs
```

## Install `cc` as build-dependency

```toml
# Cargo.toml file

[build-dependencies]
cc = "1.2.38"
```

---

## Create `build.rs` file

```rust
// build.rs file
fn main() {
    // Tell Cargo that if the given file changes, to rerun this build script.
    println!("cargo::rerun-if-changed=src/hello.c");
    // Use the `cc` crate to build a C file and statically link it.
    cc::Build::new().file("src/hello.c").compile("hello");
}
```

---

## Create `C` file

```c
// src/hello.c file
int add(int a, int b) {
    return a + b;
}
```

---

## `src/main.rs` file

```rust
// Tell Rust there’s an external C function called `add`.
// Signature must match the C function exactly.
unsafe extern "C" {
    unsafe fn add(a: i32, b: i32) -> i32;
}

fn main() {
    let result = unsafe { add(2, 3) };
    println!("2 + 3 = {}", result);
}
```
