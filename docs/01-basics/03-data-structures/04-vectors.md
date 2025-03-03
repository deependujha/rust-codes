# Vectors

- `Vec<T>` is a resizable array type in Rust
- Elements are stored in contiguous memory
- Can only store elements of the same type
- Zero-cost abstraction over arrays

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
