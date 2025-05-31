Perfect — here’s a focused and clear set of notes on **Structs and Methods** in Rust, just like your earlier style.

---

# 🏗️ Rust Structs & Methods

---

## 📦 Define a Struct

```rust
struct Person {
    name: String,
    age: u8,
}
```

---

## 🧱 Create an Instance

```rust
let user = Person {
    name: String::from("Alice"),
    age: 30,
};
```

---

## 🧠 Access Fields

```rust
println!("{} is {} years old", user.name, user.age);
```

> You can use dot syntax like `user.age`.

---

## 🛠️ Define Methods with `impl`

```rust
impl Person {
    // Method with &self
    fn greet(&self) {
        println!("Hi, I'm {}!", self.name);
    }

    // Method that returns a value
    fn is_adult(&self) -> bool {
        self.age >= 18
    }

    // Associated function (like static)
    fn new(name: &str, age: u8) -> Self {
        Person {
            name: name.to_string(),
            age,
        }
    }
}
```

---

## 🔨 Use Methods

```rust
let alice = Person::new("Alice", 30);
alice.greet();
println!("Is adult? {}", alice.is_adult());
```

---

## 🎯 Mutable Method with `&mut self`

```rust
impl Person {
    fn have_birthday(&mut self) {
        self.age += 1;
    }
}
```

```rust
let mut bob = Person::new("Bob", 25);
bob.have_birthday();
```

---

## 🧱 Tuple Structs

```rust
struct Color(u8, u8, u8);

let red = Color(255, 0, 0);
println!("R: {}", red.0);
```

---

## 🪶 Unit Structs

```rust
struct Marker;

let m = Marker;
```

> Used when you just want a type without data.

---

## 🧬 Struct Update Syntax

```rust
let person1 = Person {
    name: String::from("Alice"),
    age: 25,
};

let person2 = Person {
    name: String::from("Bob"),
    ..person1
};
```

> Moves fields from `person1`. Only works with non-borrowed types or if fields implement `Clone`.

---

## 📌 Derive Debug & Traits

```rust
#[derive(Debug, Clone, Copy)]
struct Point {
    x: i32,
    y: i32,
}
```

```rust
let p = Point { x: 3, y: 4 };
println!("{:?}", p);
```

---

Let me know if you want to go into enums, traits, or pattern matching on structs next!
