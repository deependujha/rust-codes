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

## **1️⃣ Basic Random Number & Char Generation**

```rust
use rand::random;
use rand::Rng;

fn main() {
    let random_u8: u8 = random(); // Generates a random u8
    let random_bool: bool = random(); // Generates a random bool
    let random_float: f32 = random(); // Generates a random float

    println!("Random u8: {}", random_u8);
    println!("Random bool: {}", random_bool);
    println!("Random float: {}", random_float);

    // Get an RNG:
    let mut rng = rand::rng();

    // Try printing a random unicode code point (probably a bad idea)!
    println!("char: '{}'", rng.random::<char>());
    // Try printing a random alphanumeric value instead!
    println!("alpha: '{}'", rng.sample(rand::distr::Alphanumeric) as char);
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
    let mut rng = rand::rng(); // Get the thread-local RNG

    let num: i32 = rng.random(); // Random i32
    let num_range: i32 = rng.random_range(1..=100); // Random i32 in range 1-100
    let float_range: f32 = rng.random_range(5.5..=7.5); // Random f32 in range 5.5-7.5

    println!("Random i32: {}", num);
    println!("Random i32 in range 1-100: {}", num_range);
    println!("Random i32 in range 5.5-7.5: {}", float_range);
}
```

!!! note
    The above code in section:1 is just a shorthand for `rng.random` (section:2).

| Method | Description |
|--------|------------|
| `.gen::<T>()` | Generates a random value of type `T` |
| `.gen_range(a..b)` | Generates a number in range `[a, b)` |
| `.gen_range(a..=b)` | Generates a number in range `[a, b]` |

---

## **3️⃣ Random Choice from a List**

```rust
use rand::seq::IndexedRandom;

fn main() {
    let choices = ["🍎", "🍌", "🍊", "🍉"];
    let mut rng = rand::rng();

    for _ in 1..10 {
        if let Some(random_fruit) = choices.choose(&mut rng) {
            println!("Random fruit: {}", random_fruit);
        }
    }

    println!("============");
    let my_choices = choices.choose_multiple(&mut rng, 3);
    for f in my_choices {
        println!("{f}")
    }
}
```

| Method | Description |
|--------|------------|
| `.choose(&mut rng)` | Picks a random element from a slice |
| `.choose_multiple(&mut rng, amount: usize)` | Picks multiple random element from a slice |

---

## **4️⃣ Shuffling a List**

```rust
use rand::seq::SliceRandom;

fn main() {
    let mut choices = ["🍎", "🍌", "🍊", "🍉"];
    let mut rng = rand::rng();

    for _ in 1..5 {
        choices.shuffle(&mut rng);

        println!("{choices:?}");
        println!("=======");
    }
}
```

| Method | Description |
|--------|------------|
| `.shuffle(&mut rng)` | Shuffles elements in-place |

---

## **5️⃣ Randomly Sampling Elements**

- Pick 3 unique indices from range 0-9.

```rust
use rand::seq::index::sample;

fn main() {
    let mut rng = rand::rng();
    let indices = sample(&mut rng, 10, 3); // Pick 3 unique indices from range 0-9

    println!("Random indices: {:?}", indices.into_iter().collect::<Vec<_>>());
}
```

| Method | Description |
|--------|------------|
| `.sample(&mut rng, total, count)` | Selects `count` unique elements from `total` |

---

## **6️⃣ Using a Seeded RNG for Reproducibility**

```rust
use rand::rngs::StdRng;
use rand::{Rng, SeedableRng};

fn main() {
    let seed = [42; 32]; // A fixed seed (must be 32 bytes)
    let mut rng = StdRng::from_seed(seed);

    println!("Seeded random number: {}", rng.random::<u32>());
    println!("Seeded random number: {}", rng.random::<u32>());
    println!("Seeded random number: {}", rng.random::<u32>());
}
```

- Everytime you run this executable, the generated random number in each run will be same following the order.

| Method | Description |
|--------|------------|
| `.from_seed(seed)` | Creates a seeded RNG |

---

## **7️⃣ Random Boolean Generation**

- Return a bool with a probability p of being true.

```rust
use rand;
fn main() {
    
    let mut count: u32 = 0;
    for _ in 1..100{
        let guess = rand::random_bool(0.3);

        println!("Random guess is: {guess}");
        if guess{
            count+=1;
        }
    }
    println!("Got it correct for: {count} out of 100.")
}
```

| Method | Description |
|--------|------------|
| `.random_bool(p)` | Returns `true` with probability `p` |

---

## **8️⃣ Custom Probability Distributions**

```rust
use rand::distr::{Bernoulli, Distribution};

fn main() {
    let mut rng = rand::rng();
    let bernoulli_experiment = Bernoulli::new(0.34).unwrap();

    for _ in 1..6 {
        let random_value = bernoulli_experiment.sample(&mut rng);

        println!("Bernoulli experiment random value: {}", random_value);
    }
}
```

| Method | Description |
|--------|------------|
| `Bernoulli::new(mean, std_dev)` | Creates a Bernoulli distribution |
| `.sample(&mut rng)` | Generates a sample |
