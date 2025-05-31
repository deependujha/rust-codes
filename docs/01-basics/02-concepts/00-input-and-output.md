# Rust I/O Basics: Output, and Input

## Printing to Console


```rust

//  `println!` — Print with newline

println!("Hello, world!");

// Print variables with placeholders
let name = "Alice";
let age = 30;
println!("{} is {} years old.", name, age);

// Named placeholders
println!("{name} is {age} years old.", name=name, age=age);

// Debug formatting with `{:?}`
let arr = [1, 2, 3];
println!("Array: {:?}", arr);

// ----

// `print!` — Print without newline

print!("Hello ");
print!("World!");

```

---

## Taking Input from User

Use the standard library’s `std::io` module.

!!! info
    * **In Rust, all input from `stdin` initially comes as a `String`.**
    * You then **manually parse** that string into the type you want (like `i32`, `f64`, `bool`, etc.) using `.parse()` with proper error handling.
    * There’s no built-in operator like C++’s `cin >> n` that automatically reads and converts the input to the variable’s type.

    > This explicit parsing makes Rust’s input handling a bit more verbose but safer and clearer in intent.

---

- **Note**: In rust, you always read a whole line as a `String` and then it's up to you to parse it into the desired type.


### Read a line from standard input

```rust
use std::io;

fn main() {
    let mut input = String::new();

    println!("Enter your name:");

    io::stdin()
        .read_line(&mut input)
        .expect("Failed to read line");

    let name = input.trim(); // Remove newline
    println!("Hello, {}!", name);
}
```

### Parse input into numbers

```rust
use std::io;

fn main() {
    let mut input = String::new();

    println!("Enter a number:");

    io::stdin()
        .read_line(&mut input)
        .expect("Failed to read line");

    let num: i32 = input.trim().parse().expect("Please type a number!");
    println!("You typed: {}", num);
}
```

---

### Read multiple words separately

```rust
use std::io;

fn main() {
    let mut input = String::new();
    io::stdin().read_line(&mut input).expect("Failed to read line");

    let words: Vec<&str> = input.split_whitespace().collect();
    println!("Words: {:?}", words);
}
```

---

### Read multiple integers in one line

```rust
use std::io;

fn main() {
    let mut input = String::new();

    println!("Enter multiple integers separated by spaces:");

    io::stdin().read_line(&mut input).expect("Failed to read line");

    let numbers: Vec<i32> = input
        .split_whitespace()
        .map(|s| s.parse().expect("Please enter valid integers"))
        .collect();

    println!("You entered: {:?}", numbers);
}
```
