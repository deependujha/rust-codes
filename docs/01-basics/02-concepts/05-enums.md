# 🎭 Rust Enums

Enums allow you to define a type by enumerating its possible *variants*.

---

## 📦 Basic Enum

```rust
enum Direction {
    North,
    South,
    East,
    West,
}
```

### ✅ Use It

```rust
let dir = Direction::North;
```

---

## 📐 Enum with Data

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(u8, u8, u8),
}
```

> Variants can hold data (like structs or tuples).

### ✅ Use It

```rust
let msg1 = Message::Write(String::from("Hello"));
let msg2 = Message::Move { x: 10, y: 20 };
```

---

## 🧠 Match on Enum

```rust
match msg1 {
    Message::Write(text) => println!("Message: {}", text),
    Message::Move { x, y } => println!("Move to ({}, {})", x, y),
    Message::Quit => println!("Quit!"),
    Message::ChangeColor(r, g, b) => println!("Color: {}, {}, {}", r, g, b),
}
```

---

## 📚 Enum with Methods

```rust
impl Message {
    fn call(&self) {
        match self {
            Message::Quit => println!("Quit"),
            Message::Move { x, y } => println!("Move to ({}, {})", x, y),
            Message::Write(text) => println!("Write: {}", text),
            Message::ChangeColor(r, g, b) => println!("Color: {}, {}, {}", r, g, b),
        }
    }
}
```

---

## 🪙 Enums with `Option` & `Result`

Rust’s standard library uses enums heavily:

```rust
let maybe_num: Option<i32> = Some(5);
let none_num: Option<i32> = None;

let result: Result<i32, &str> = Ok(10);
let error: Result<i32, &str> = Err("Oops");
```

---

## 🧪 Matching on Option

```rust
match maybe_num {
    Some(n) => println!("Got: {}", n),
    None => println!("Nothing here"),
}
```

## 🔍 Matching on Result

```rust
match result {
    Ok(v) => println!("Success: {}", v),
    Err(e) => println!("Error: {}", e),
}
```

---

## 📌 Derive Debug

```rust
#[derive(Debug)]
enum Status {
    Online,
    Offline,
}
```

---

## 🔥 `if-let` Syntax

```rust
    let config_max = Some(3u8);
    match config_max {
        Some(max) => println!("The maximum is configured to be {max}"),
        _ => (),
    }
```

can be simplified with `if let`:

```rust
    let config_max = Some(3u8);
    if let Some(max) = config_max {
        println!("The maximum is configured to be {max}");
    }
```

---

## 🙌🏻 `let-else` Syntax

- original code:

```rust
fn describe_state_quarter(coin: Coin) -> Option<String> {
    let state = if let Coin::Quarter(state) = coin {
        state
    } else {
        return None;
    };

    if state.existed_in(1900) {
        Some(format!("{state:?} is pretty old, for America!"))
    } else {
        Some(format!("{state:?} is relatively new."))
    }
}
```

- can be simplified with `let-else`:

```rust
fn describe_state_quarter(coin: Coin) -> Option<String> {
    let Coin::Quarter(state) = coin else {
        return None;
    };

    if state.existed_in(1900) {
        Some(format!("{state:?} is pretty old, for America!"))
    } else {
        Some(format!("{state:?} is relatively new."))
    }
}
```

---

## ❓ The `?` Operator in Rust

The `?` operator is used to **propagate errors** in functions that return a `Result` or `Option`.

---

### ✅ For `Result`

```rust
fn read_file() -> Result<String, std::io::Error> {
    let content = std::fs::read_to_string("file.txt")?; // if error, returns it early
    Ok(content)
}
```

* If `read_to_string` returns `Ok(val)`, `val` is assigned.
* If it returns `Err(e)`, the whole function returns `Err(e)` early.

Equivalent to:

```rust
let content = match std::fs::read_to_string("file.txt") {
    Ok(c) => c,
    Err(e) => return Err(e),
};
```

---

### ✅ For `Option`

```rust
fn get_first_char(s: &str) -> Option<char> {
    let first = s.chars().next()?; // If None, return None early
    Some(first)
}
```

---

### ✅ Requirements

* The function must return a `Result<_, E>` or `Option<_>`.
* The error type must implement `From` trait (auto for most common errors).
* You can only use `?` inside functions that return compatible types.

---

### 🧠 Good For:

* Cleaner error handling.
* Early returns on error without boilerplate.

---

### TL;DR

| Use Case | Behavior                       |
| -------- | ------------------------------ |
| `Result` | Return `Err(e)` early if error |
| `Option` | Return `None` early if `None`  |

Use `?` to write cleaner, less nested code when dealing with fallible operations.
