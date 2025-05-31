# 🧠 Rust Primitive Data Types

Rust has **scalar** and **compound** types:

---

## ✅ Scalar Types

| Type             | Description                    | Example Values         |
| ---------------- | ------------------------------ | ---------------------- |
| `i8`..`i128`     | Signed integers (8–128 bits)   | `-42`, `0`, `127`      |
| `u8`..`u128`     | Unsigned integers (8–128 bits) | `0`, `255`, `100`      |
| `isize`, `usize` | Pointer-sized int types        | Architecture dependent |
| `f32`, `f64`     | Floating-point numbers         | `3.14`, `-2.0`         |
| `bool`           | Boolean                        | `true`, `false`        |
| `char`           | Unicode scalar value (4 bytes) | `'a'`, `'∞'`, `'🚀'`   |

> Default types: `i32` for integers, `f64` for floats, `char` for characters.

---

## 📦 Compound Types

### 1. **Tuple**

Fixed-size collection of values (can be of different types).

```rust
let tup: (i32, f64, char) = (42, 3.14, 'z');
let (x, y, z) = tup; // Destructuring
println!("{}", tup.0); // Access with index
```

### 2. **Array**

Fixed-size collection of values of the **same type**.

```rust
let arr: [i32; 3] = [1, 2, 3];
println!("{}", arr[0]); // Indexing
```

> Arrays are stack-allocated and have fixed length.

---

## 🔍 Type Inference and Annotation

Rust can infer types, but you can also explicitly specify:

```rust
let a = 10;           // inferred as i32
let b: u64 = 20;      // explicitly u64
let pi: f32 = 3.14;
let truth: bool = true;
```

---

## 🧪 Type Casting (Safe & Explicit)

Use `as` for explicit casting:

```rust
let x: i32 = 42;
let y: f64 = x as f64;
```

> No implicit casting between types (unlike C/C++). Always explicit!

---

## 🛡 Common Traits on Primitives

| Trait     | Use Case                    |
| --------- | --------------------------- |
| `Copy`    | Duplicates value (no move)  |
| `Clone`   | Makes an explicit deep copy |
| `Debug`   | Printable with `{:?}`       |
| `Default` | Provides sensible default   |

---

## ⚠️ Overflow & Special Cases in Rust Primitives

### 🔹 Integer Overflow

| Mode        | Behavior               |
| ----------- | ---------------------- |
| **Debug**   | ❌ Panic on overflow    |
| **Release** | ✅ Wrap around silently |

#### Example (Debug mode):

```rust
fn main() {
    let x: u8 = 255;
    let y = x + 1; // 💥 Panic: attempted to add with overflow
}
```

#### Example (Release mode):

```rust
fn main() {
    let x: u8 = 255;
    let y = x.wrapping_add(1); // ✅ y = 0 (wraps around)
}
```

---

### 🔹 Safe Integer Operations

| Method               | Description                          |
| -------------------- | ------------------------------------ |
| `wrapping_add(x)`    | Wraps around on overflow             |
| `checked_add(x)`     | Returns `None` if overflow occurs    |
| `saturating_add(x)`  | Clamps to max/min if overflow occurs |
| `overflowing_add(x)` | Returns `(value, overflow: bool)`    |

```rust
let a: u8 = 250;
println!("{}", a.wrapping_add(10));     // → 4
println!("{:?}", a.checked_add(10));    // → None
println!("{}", a.saturating_add(10));   // → 255
println!("{:?}", a.overflowing_add(10));// → (4, true)
```

---

### 🔹 Signed Integers

Signed integers (like `i8`, `i32`) overflow just like unsigned ones:

```rust
let x: i8 = 127;
let y = x.wrapping_add(1); // y = -128 (wraps around)
```

---

### 🔹 Floating Point (f32, f64)

#### No panics, but:

| Concept            | Description                             |
| ------------------ | --------------------------------------- |
| Overflow           | Becomes `inf` or `-inf`                 |
| Underflow          | Becomes `0.0` or denormalized subnormal |
| Division by zero   | Becomes `inf` or `NaN`                  |
| Invalid operations | Produce `NaN`                           |

```rust
let a = 1e308f64;
println!("{}", a * 10.0); // inf

let b = 0.0f64;
println!("{}", 1.0 / b);  // inf

let c: f64 = 0.0 / 0.0;
println!("{}", c.is_nan()); // true
```

---

### 🔹 Summary Table

| Type         | Panics on Overflow | Wraps     | Special Values       |
| ------------ | ------------------ | --------- | -------------------- |
| `u8`, `i32`  | ✅ Debug mode       | ✅ Release | -                    |
| `f32`, `f64` | ❌ Never panics     | N/A       | `NaN`, `inf`, `-inf` |
