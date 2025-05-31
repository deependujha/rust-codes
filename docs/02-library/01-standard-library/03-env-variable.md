# 🌱 Rust Environment Variables (`std::env`)

The `std::env` module allows interacting with the environment at runtime: reading, setting, modifying env vars, accessing CLI args, current dir, etc.

---

## ✅ Get Environment Variable

```rust
use std::env;

fn main() {
    // Returns Result<String, VarError>
    match env::var("HOME") {
        Ok(val) => println!("HOME: {}", val),
        Err(e) => println!("Couldn't read HOME: {}", e),
    }
}
```

---

## ❌ Handle Missing Env Var Gracefully

```rust
let path = env::var("MY_CONFIG_PATH").unwrap_or("default/path".to_string());
```

---

## 🔧 Set an Env Var

> Only lasts during the current process lifetime

```rust
env::set_var("MY_KEY", "my_value");
println!("{}", env::var("MY_KEY").unwrap()); // prints "my_value"
```

---

## 🚫 Remove an Env Var

```rust
env::remove_var("MY_KEY");
```

---

## 📜 List All Env Vars

```rust
for (key, value) in env::vars() {
    println!("{key} = {value}");
}
```

---

## 🧾 Read CLI Args

```rust
fn main() {
    for arg in env::args() {
        println!("{}", arg);
    }
}
```

---

## 📂 Get Current Directory

```rust
let path = env::current_dir().unwrap();
println!("Current dir: {}", path.display());
```

---

## 🔁 Set Current Directory

```rust
env::set_current_dir("/tmp").expect("Failed to change directory");
```
