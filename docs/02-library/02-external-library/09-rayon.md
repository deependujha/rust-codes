# Rayon Notes

## 1. Introduction to Rayon

Rayon is a Rust library that provides an easy way to parallelize computations using data parallelism. It works by converting standard iterators into parallel iterators.

## 2. Setting Up Rayon

To use Rayon, add it to your `Cargo.toml`:

```toml
[dependencies]
rayon = "1"
```

## 3. Converting Iterators to Parallel Iterators

Rayon provides `par_iter()` to turn an iterator into a parallel iterator.

```rust
use rayon::prelude::*;

fn main() {
    let nums = vec![1, 2, 3, 4, 5];
    let squares: Vec<_> = nums.par_iter().map(|x| x * x).collect();
    println!("{:?}", squares);
}
```

## 4. Parallel For-Each

Use `par_iter().for_each()` to process items in parallel without collecting results.

```rust
use rayon::prelude::*;

fn main() {
    let nums = vec![1, 2, 3, 4, 5];
    nums.par_iter().for_each(|x| println!("Processing: {}", x));
}
```

## 5. Parallel Sorting

Rayon provides parallel sorting with `par_sort()` and `par_sort_unstable()`.

```rust
use rayon::prelude::*;

fn main() {
    let mut nums = vec![5, 3, 1, 4, 2];
    nums.par_sort();
    println!("{:?}", nums);
}
```

## 6. Parallel Filtering

Parallel filtering with `par_filter()`.

```rust
use rayon::prelude::*;

fn main() {
    let nums = vec![1, 2, 3, 4, 5, 6];
    let even_nums: Vec<_> = nums.par_iter().filter(|&&x| x % 2 == 0).collect();
    println!("{:?}", even_nums);
}
```

## 7. Parallel Reduction

Use `reduce()` to perform reductions in parallel.

```rust
use rayon::prelude::*;

fn main() {
    let nums = vec![1, 2, 3, 4, 5];
    let sum = nums.par_iter().cloned().reduce(|| 0, |a, b| a + b);
    println!("Sum: {}", sum);
}
```

## 8. Parallelizing Custom Collections

To enable parallel iteration on custom collections, implement `IntoParallelIterator`.

```rust
use rayon::prelude::*;
use std::collections::HashMap;

fn main() {
    let mut map = HashMap::new();
    map.insert("a", 1);
    map.insert("b", 2);
    map.insert("c", 3);
    
    map.par_iter().for_each(|(k, v)| {
        println!("Key: {}, Value: {}", k, v);
    });
}
```

## 9. Using `par_bridge()` for Non-Rayon Iterators

If an iterator does not implement `ParallelIterator`, you can convert it using `par_bridge()`.

```rust
use rayon::prelude::*;

fn main() {
    let nums = (0..10).into_iter().par_bridge().map(|x| x * 2).collect::<Vec<_>>();
    println!("{:?}", nums);
}
```

## 10. Controlling Thread Pool

Use `rayon::ThreadPoolBuilder` to customize thread pool settings.

```rust
use rayon::ThreadPoolBuilder;

fn main() {
    let pool = ThreadPoolBuilder::new().num_threads(4).build().unwrap();
    pool.install(|| {
        let sum: i32 = (1..10).into_par_iter().sum();
        println!("Sum: {}", sum);
    });
}
```
