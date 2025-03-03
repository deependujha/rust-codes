# Vectors

- `Vec<T>` is a resizable array type in Rust
- Elements are stored in contiguous memory
- Can only store elements of the same type
- Zero-cost abstraction over arrays

---

## Rust `Range Iterator`

```rust
for i in 0..5 {
    println!("{}", i);
}
// Output: 0 1 2 3 4

for i in 0..=5 {
    println!("{}", i);
}
// Output: 0 1 2 3 4 5

let numbers: Vec<i32> = (0..5).collect();
println!("{:?}", numbers);
// Output: [0, 1, 2, 3, 4]

// ------- reverse -------

let numbers: Vec<i32> = (0..5).rev().collect();
println!("{:?}", numbers);
// Output: [4, 3, 2, 1, 0]

for i in (0..5).rev() {
    println!("{}", i);
}

// ------- step by -------

let numbers: Vec<i32> = (0..10).step_by(2).collect();
println!("{:?}", numbers);
// Output: [0, 2, 4, 6, 8]

// ------- take -------

for i in (0..).take(5) {  // Infinite range, but taking first 5 elements
    println!("{}", i);
}
// Output: 0 1 2 3 4

// ------- filter -------

let odd_numbers: Vec<i32> = (0..10).filter(|x| x % 2 == 1).collect();
println!("{:?}", odd_numbers);
// Output: [1, 3, 5, 7, 9]

// ------- chain -------

let numbers: Vec<i32> = (0..3).chain(7..10).collect();
println!("{:?}", numbers);
// Output: [0, 1, 2, 7, 8, 9]

// ------- array slicing -------

let arr = [10, 20, 30, 40, 50];
let slice = &arr[1..4]; // Indexing works with ranges

println!("{:?}", slice);
// Output: [20, 30, 40]
```

### **Summary of Range Features**

| Rust Range | Equivalent Python | Explanation |
|------------|------------------|-------------|
| `0..5` | `range(5)` | From `0` to `4` (exclusive) |
| `0..=5` | `range(6)` | From `0` to `5` (inclusive) |
| `(0..5).collect()` | `list(range(5))` | Converts to `Vec<i32>` |
| `(0..5).rev()` | `reversed(range(5))` | Reverse order |
| `(0..10).step_by(2)` | `range(0, 10, 2)` | Steps of `2` |
| `(0..).take(5)` | `itertools.islice(itertools.count(0), 5)` | Infinite iterator, taking first 5 values |
| `(0..10).filter(|x| x % 2 == 1)` | `[x for x in range(10) if x % 2 == 1]` | Filtering elements |
| `(0..3).chain(7..10)` | `list(range(3)) + list(range(7,10))` | Chaining ranges |

---

## Creating Vectors

### Empty Vector

```rust
let mut nums: Vec<i32> = Vec::new();
// or with type inference
let mut nums = vec![];
```

### With Initial Values

```rust
// Using vec! macro
let nums = vec![1, 2, 3, 4, 5];

// From array
let nums = Vec::from([1, 2, 3, 4, 5]);

// converting array to vector
let arr = [1, 2, 3, 4, 5];
let nums = arr.to_vec();

// With repeated values
let zeros = vec![0; 5]; // Creates [0, 0, 0, 0, 0]
```

---

## Common Methods

!!! info "Methods"
    - `push`: Add element to end
    - `pop`: Remove and return last element
    - `insert`: Insert element at index
    - `remove`: Remove element at index
    - `clear`: Remove all elements
    - `len`: Get length
    - `capacity`: Get current capacity
    - `is_empty`: Check if empty
    - `get`: Get reference to element
    - `first`/`last`: Get first/last element

```rust
fn main() {
    let mut nums = Vec::new();
    
    // Adding elements
    nums.push(1);
    nums.push(2);
    nums.push(3);
    
    // Accessing elements
    let second = nums[1]; // Direct access
    let second = nums.get(1); // Safe access (returns Option)
    
    // Removing elements
    let last = nums.pop(); // removes and returns 3
    nums.remove(0); // removes 1
    
    // Info
    let length = nums.len();
    let is_empty = nums.is_empty();
    
    // Clear
    nums.clear();
}
```

---

## 2D Vectors

### Creating 2D Vectors

```rust
// Empty 2D vector
let mut grid: Vec<Vec<i32>> = Vec::new();

// Initialize with size
let rows = 3;
let cols = 4;
let grid = vec![vec![0; cols]; rows];

// With values
let matrix = vec![
    vec![1, 2, 3],
    vec![4, 5, 6],
    vec![7, 8, 9]
];
```

### Working with 2D Vectors

```rust
fn main() {
    let mut grid = vec![vec![0; 3]; 3];
    
    // Modify element
    grid[0][1] = 5;
    
    // Access element
    let val = grid[1][2];
    
    // Iterate over 2D vector
    for row in &grid {
        for &cell in row {
            print!("{} ", cell);
        }
        println!();
    }
    
    // Add new row
    grid.push(vec![1, 1, 1]);
    
    // Get dimensions
    let rows = grid.len();
    let cols = grid[0].len();
}
```

!!! tip "Remember"
    - Each row can have different lengths (jagged array)
    - Use `grid[row][col]` for access
    - Be careful with bounds when accessing elements

---

## Safe way to set a value at some index

```rust
let mut numbers = vec![1, 2, 3, 4, 5];
if let Some(value) = numbers.get_mut(2) {  // Safe: Check if index exists
    *value = 99;  // Modify safely
}
println!("{:?}", numbers); // Output: [1, 2, 99, 4, 5]

// ----- another way -----
let mut numbers = vec![1, 2, 3, 4, 5];
*numbers.get_mut(2).expect("Index out of bounds") = 99;
println!("{:?}", numbers); // Output: [1, 2, 99, 4, 5]
```

---

### Iterating over a vector

!!! info "Methods"
    - `iter`: Get immutable iterator
    - `iter_mut`: Get mutable iterator
    - `enumerate`: Get index and value
    - `all`: Check if all elements satisfy a condition
    - `any`: Check if any element satisfies a condition
    - `find`: Find first element satisfying a condition
    - `fold`: Aggregate elements
    - `map`: Apply a function to each element
    - `filter`: Filter elements
    - `collect`: Collect elements into a new collection

```rust
let nums = vec![1, 2, 3, 4, 5];

for num in nums {
    println!("{num}");
}

// ----- another way -----
for i in 0..nums.len() {
    println!("{}", nums[i]);
}

// ----- another way -----
for num in nums.iter() {
    println!("{num}");
}

// ----- another way -----
for (i, num) in nums.iter().enumerate() {
    println!("{}: {}", i, num);
}

// ----- another way -----
for num in nums.iter_mut() {
    *num *= 2;
}
```

---

### Filtering elements

```rust
let nums = vec![1, 2, 3, 4, 5];

let even_nums = nums.iter().filter(|&num| num % 2 == 0).collect::<Vec<_>>();

println!("{:?}", even_nums); // Output: [2, 4]
```

---

### Mapping elements

```rust
let nums = vec![1, 2, 3, 4, 5];

let squares = nums.iter().map(|num| num * num).collect::<Vec<_>>();

println!("{:?}", squares); // Output: [1, 4, 9, 16, 25]
```

---

### Aggregating elements

```rust
let nums = vec![1, 2, 3, 4, 5];

let sum = nums.iter().sum::<i32>();

println!("{}", sum); // Output: 15
```

---

### Finding elements

```rust
let nums = vec![1, 2, 3, 4, 5];

let first_even = nums.iter().find(|&num| num % 2 == 0);

println!("{:?}", first_even); // Output: Some(2)
```

---

### Sorting elements

```rust
let mut nums = vec![3, 1, 2, 4, 5];

nums.sort();

println!("{:?}", nums); // Output: [1, 2, 3, 4, 5]
```

---

### Removing elements

```rust
let mut nums = vec![1, 2, 3, 4, 5];

nums.remove(0); // removes 1

println!("{:?}", nums); // Output: [2, 3, 4, 5]
```

---

### Removing duplicates

```rust
let mut nums = vec![1, 2, 2, 3, 4, 4, 5];

nums.dedup();
println!("{:?}", nums); // Output: [1, 2, 3, 4, 5]
```

---

### `All` & `Any`

```rust
let nums = vec![1, 2, 3, 4, 5];

let all_even = nums.iter().all(|&num| num % 2 == 0);
let any_even = nums.iter().any(|&num| num % 2 == 0);

println!("{}", all_even); // Output: false
println!("{}", any_even); // Output: true
```
