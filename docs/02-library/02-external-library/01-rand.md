# **📌 Rust `rand` Cheat Sheet**  

The `rand` crate provides **random number generation**, including **secure RNGs, seeding, distributions, and shuffling**.

## **📦 Installation**

- Add `rand` to `Cargo.toml`

```toml
[dependencies]
rand = "0.8"
```

or,

- Run commannd:

```bash
cargo add rand
```

---

## **1️⃣ Basic Random Number Generation**

```rust
use rand::random;

fn main() {
    let random_u8: u8 = random(); // Generates a random u8
    let random_bool: bool = random(); // Generates a random bool

    println!("Random u8: {}", random_u8);
    println!("Random bool: {}", random_bool);
}
```

| Method | Description |
|--------|------------|
| `random::<T>()` | Generates a random value of type `T` |

---

## **2️⃣ Using a Random Number Generator (RNG)**

```rust
use rand::Rng;

fn main() {
    let mut rng = rand::thread_rng(); // Get the thread-local RNG

    let num: i32 = rng.gen(); // Random i32
    let num_range: i32 = rng.gen_range(1..=100); // Random i32 in range 1-100

    println!("Random i32: {}", num);
    println!("Random i32 in range 1-100: {}", num_range);
}
```

| Method | Description |
|--------|------------|
| `.gen::<T>()` | Generates a random value of type `T` |
| `.gen_range(a..b)` | Generates a number in range `[a, b)` |
| `.gen_range(a..=b)` | Generates a number in range `[a, b]` |

---

## **3️⃣ Random Floating-Point Numbers**

```rust
use rand::Rng;

fn main() {
    let mut rng = rand::thread_rng();

    let f1: f32 = rng.gen(); // Random f32 in range [0.0, 1.0)
    let f2: f64 = rng.gen_range(5.5..10.5); // Random f64 in range [5.5, 10.5)

    println!("Random f32: {}", f1);
    println!("Random f64 in range 5.5-10.5: {}", f2);
}
```

| Method | Description |
|--------|------------|
| `.gen::<f32>()` | Generates a float in `[0.0, 1.0)` |
| `.gen_range(a..b)` | Generates a float in `[a, b)` |

---

## **4️⃣ Random Choice from a List**

```rust
use rand::seq::SliceRandom;

fn main() {
    let choices = ["🍎", "🍌", "🍊", "🍉"];
    let mut rng = rand::thread_rng();

    if let Some(random_fruit) = choices.choose(&mut rng) {
        println!("Random fruit: {}", random_fruit);
    }
}
```

| Method | Description |
|--------|------------|
| `.choose(&mut rng)` | Picks a random element from a slice |

---

## **5️⃣ Shuffling a List**

```rust
use rand::seq::SliceRandom;

fn main() {
    let mut numbers = [1, 2, 3, 4, 5];
    let mut rng = rand::thread_rng();

    numbers.shuffle(&mut rng);
    println!("Shuffled: {:?}", numbers);
}
```

| Method | Description |
|--------|------------|
| `.shuffle(&mut rng)` | Shuffles elements in-place |

---

## **6️⃣ Randomly Sampling Elements**

```rust
use rand::seq::index::sample;

fn main() {
    let mut rng = rand::thread_rng();
    let indices = sample(&mut rng, 10, 3); // Pick 3 unique indices from range 0-9

    println!("Random indices: {:?}", indices.into_iter().collect::<Vec<_>>());
}
```

| Method | Description |
|--------|------------|
| `.sample(&mut rng, total, count)` | Selects `count` unique elements from `total` |

---

## **7️⃣ Secure Random Numbers (Cryptographic RNG)**

```rust
use rand::rngs::OsRng;
use rand::RngCore;

fn main() {
    let mut rng = OsRng;
    let mut bytes = [0u8; 16]; // 16 random bytes

    rng.fill_bytes(&mut bytes);
    println!("Secure random bytes: {:?}", bytes);
}
```

| Method | Description |
|--------|------------|
| `OsRng` | Cryptographically secure RNG |
| `.fill_bytes(&mut buf)` | Fills a buffer with random bytes |

---

## **8️⃣ Using a Seeded RNG for Reproducibility**

```rust
use rand::{SeedableRng, Rng};
use rand::rngs::StdRng;

fn main() {
    let seed = [42; 32]; // A fixed seed (must be 32 bytes)
    let mut rng = StdRng::from_seed(seed);

    println!("Seeded random number: {}", rng.gen::<u32>());
}
```

| Method | Description |
|--------|------------|
| `.from_seed(seed)` | Creates a seeded RNG |

---

## **9️⃣ Random Boolean Generation**

```rust
use rand::Rng;

fn main() {
    let mut rng = rand::thread_rng();

    let coin_flip = rng.gen_bool(0.5); // 50% chance of true
    println!("Coin flip result: {}", coin_flip);
}
```

| Method | Description |
|--------|------------|
| `.gen_bool(p)` | Returns `true` with probability `p` |

---

## **🔟 Custom Probability Distributions**

```rust
use rand::distributions::{Distribution, Normal};

fn main() {
    let mut rng = rand::thread_rng();
    let normal = Normal::new(10.0, 2.0).unwrap(); // Mean = 10, Std Dev = 2

    let random_value = normal.sample(&mut rng);
    println!("Normally distributed value: {}", random_value);
}
```

| Method | Description |
|--------|------------|
| `Normal::new(mean, std_dev)` | Creates a normal distribution |
| `.sample(&mut rng)` | Generates a sample |

---

## **📌 Summary**

| Category | Method |
|----------|--------|
| **Basic RNG** | `random::<T>()`, `.gen::<T>()` |
| **Ranged Values** | `.gen_range(a..b)`, `.gen_range(a..=b)` |
| **Lists** | `.choose(&mut rng)`, `.shuffle(&mut rng)` |
| **Sampling** | `.sample(&mut rng, total, count)` |
| **Secure RNG** | `OsRng.fill_bytes(&mut buf)` |
| **Seeding** | `StdRng::from_seed(seed)` |
| **Booleans** | `.gen_bool(prob)` |
| **Distributions** | `Normal::new(mean, std_dev)` |
