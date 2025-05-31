Awesome! Here’s a clean and concise note on **Enums** in Rust, designed for quick revision and real-world coding:

---

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

Let me know if you want a follow-up on `Option`, `Result`, or custom error handling!
