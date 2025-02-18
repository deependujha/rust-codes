# 📌 Rust Iterators Cheat Sheet

## **1️⃣ Creating an Iterator**

### **Basic Iterators**

```rust
let numbers = vec![1, 2, 3, 4, 5];

// `iter()`: Immutable reference to each element
let mut iter1 = numbers.iter();
println!("{:?}", iter1.next()); // Some(1)

// `into_iter()`: Takes ownership (moves elements)
let mut iter2 = numbers.into_iter();
println!("{:?}", iter2.next()); // Some(1)
```

| Method | Description |
|--------|------------|
| `.iter()` | Returns an **immutable** iterator (`&T`) |
| `.iter_mut()` | Returns a **mutable** iterator (`&mut T`) |
| `.into_iter()` | Takes ownership and **moves** items (`T`) |

---

## **2️⃣ Consuming Iterators**

### **Methods that iterate & consume**

```rust
let numbers = vec![1, 2, 3, 4, 5];

let sum: i32 = numbers.iter().sum();
println!("Sum: {}", sum); // 15

let collected: Vec<_> = numbers.iter().collect();
println!("Collected: {:?}", collected); // [1, 2, 3, 4, 5]
```

| Method | Description |
|--------|------------|
| `.sum()` | Consumes the iterator and returns the sum |
| `.product()` | Multiplies all elements and returns the result |
| `.collect::<Vec<T>>()` | Collects elements into a vector, set, or hashmap |
| `.count()` | Returns the number of elements |

---

## **3️⃣ Transforming Iterators**

### **`.map()` - Apply a function to each item**

```rust
let numbers = vec![1, 2, 3];
let doubled: Vec<_> = numbers.iter().map(|x| x * 2).collect();
println!("{:?}", doubled); // [2, 4, 6]
```

---

## **4️⃣ Filtering Iterators**

### **`.filter()` - Keep items that satisfy a condition**

```rust
let numbers = vec![1, 2, 3, 4, 5];
let even_numbers: Vec<_> = numbers.iter().filter(|&&x| x % 2 == 0).collect();
println!("{:?}", even_numbers); // [2, 4]
```

| Method | Description |
|--------|------------|
| `.filter(|x| condition)` | Keeps elements that return `true` |
| `.filter_map(|x| Some(y))` | Like `map()`, but skips `None` values |

---

## **5️⃣ Combining Iterators**

### **`.chain()` - Concatenate two iterators**

```rust
let a = vec![1, 2, 3];
let b = vec![4, 5, 6];
let combined: Vec<_> = a.iter().chain(b.iter()).collect();
println!("{:?}", combined); // [1, 2, 3, 4, 5, 6]
```

---

## **6️⃣ Finding & Checking Elements**

```rust
let numbers = vec![1, 2, 3, 4, 5];

let any_even = numbers.iter().any(|&x| x % 2 == 0);
println!("Any even? {}", any_even); // true

let all_even = numbers.iter().all(|&x| x % 2 == 0);
println!("All even? {}", all_even); // false

let first_even = numbers.iter().find(|&&x| x % 2 == 0);
println!("First even: {:?}", first_even); // Some(2)
```

| Method | Description |
|--------|------------|
| `.any(|x| condition)` | Returns `true` if **any** element matches |
| `.all(|x| condition)` | Returns `true` if **all** elements match |
| `.find(|x| condition)` | Returns the **first** element that matches |

---

## **7️⃣ Taking & Skipping Elements**

```rust
let numbers = vec![1, 2, 3, 4, 5];

let first_two: Vec<_> = numbers.iter().take(2).collect();
println!("{:?}", first_two); // [1, 2]

let skip_two: Vec<_> = numbers.iter().skip(2).collect();
println!("{:?}", skip_two); // [3, 4, 5]
```

| Method | Description |
|--------|------------|
| `.take(n)` | Takes **first** `n` elements |
| `.skip(n)` | Skips **first** `n` elements |

---

## **8️⃣ Zipping Iterators**

### **`.zip()` - Pair two iterators together**

```rust
let a = vec![1, 2, 3];
let b = vec!['a', 'b', 'c'];
let zipped: Vec<_> = a.iter().zip(b.iter()).collect();
println!("{:?}", zipped); // [(1, 'a'), (2, 'b'), (3, 'c')]
```

---

## **9️⃣ Enumerating Items**

### **`.enumerate()` - Attach an index**

```rust
let letters = vec!['a', 'b', 'c'];
for (index, letter) in letters.iter().enumerate() {
    println!("{}: {}", index, letter);
}
// 0: a
// 1: b
// 2: c
```

---

## **🔟 Lazy vs. Consuming Iterators**

Rust iterators are **lazy**, meaning they only compute when needed.

### **Lazy Example (Does Nothing)**

```rust
let _lazy = (1..5).map(|x| x * 2);  // Does nothing
```

### **Forcing Computation (Uses `collect()`)**

```rust
let result: Vec<_> = (1..5).map(|x| x * 2).collect();
println!("{:?}", result); // [2, 4, 6, 8]
```

---

## **📌 Summary**

| Category | Methods |
|----------|---------|
| **Creating Iterators** | `.iter()`, `.iter_mut()`, `.into_iter()` |
| **Consuming Iterators** | `.sum()`, `.product()`, `.collect()`, `.count()` |
| **Transforming** | `.map(|x| ...)` |
| **Filtering** | `.filter(|x| ...)`, `.filter_map(|x| Some(y))` |
| **Combining** | `.chain()` |
| **Finding** | `.any()`, `.all()`, `.find()` |
| **Taking/Skipping** | `.take(n)`, `.skip(n)` |
| **Zipping** | `.zip()` |
| **Enumerating** | `.enumerate()` |
