# Serde & Serde JSON Notes

## 1. Introduction to Serde

Serde is a framework for **serializing and deserializing** Rust data structures efficiently.

### Adding Serde to Your Project

```toml
[dependencies]
serde = { version = "1", features = ["derive"] }
serde_json = "1"
```

## 2. Basic Serialization & Deserialization

```rust
use serde::{Serialize, Deserialize};
use serde_json;

#[derive(Serialize, Deserialize, Debug)]
struct Person {
    name: String,
    age: u8,
}

fn main() {
    let person = Person { name: "Alice".to_string(), age: 25 };
    
    // Serialization (Struct -> JSON)
    let json_str = serde_json::to_string(&person).unwrap();
    println!("Serialized: {json_str}");
    
    // Deserialization (JSON -> Struct)
    let deserialized: Person = serde_json::from_str(&json_str).unwrap();
    println!("Deserialized: {:?}", deserialized);
}
```

## 3. Using `serde_json::Value`

For working with arbitrary JSON structures:

```rust
use serde_json::Value;

fn main() {
    let data = r#"{ "name": "Bob", "age": 30 }"#;
    let v: Value = serde_json::from_str(data).unwrap();
    println!("Name: {}", v["name"]);
    println!("Age: {}", v["age"]);
}
```

## 4. Handling Optionals & Defaults

```rust
use serde::{Serialize, Deserialize};

#[derive(Serialize, Deserialize, Debug)]
struct Config {
    #[serde(default = "default_host")]
    host: String,
    port: Option<u16>,
}

fn default_host() -> String {
    "localhost".to_string()
}
```

## 5. Ignoring Fields

```rust
#[derive(Serialize, Deserialize)]
struct User {
    id: u32,
    #[serde(skip_serializing)]
    password: String,
}
```

## 6. Renaming Fields

```rust
#[derive(Serialize, Deserialize)]
struct Server {
    #[serde(rename = "ipAddress")]
    ip: String,
}
```

## 7. Serializing to Pretty JSON

```rust
let pretty_json = serde_json::to_string_pretty(&person).unwrap();
println!("{pretty_json}");
```
