# Hashmap

- `HashMap` is a collection of key-value pairs.
- Used to store data in a way that allows for fast retrieval of values by their keys.
- The keys must be `Hash` and `Eq` traits.
- The values must be `Copy` trait.

---

## Creating a HashMap

### `HashMap::new()`

```rust
use std::collections::HashMap;

// Type inference lets us omit an explicit type signature (which
// would be `HashMap<String, String>` in this example).
let mut book_reviews = HashMap::new();

// Review some books.
book_reviews.insert(
    "Adventures of Huckleberry Finn".to_string(),
    "My favorite book.".to_string(),
);
```

### `HashMap::from`

- A HashMap with a known list of items can be initialized from an array:

```rust
use std::collections::HashMap;

let solar_distance = HashMap::from([
    ("Mercury", 0.4),
    ("Venus", 0.7),
    ("Earth", 1.0),
    ("Mars", 1.5),
]);
```

---

## HashMap methods

!!! info "Methods"
    - `insert`: Insert a key-value pair into the HashMap.
    - `get`: Get a value by key.
    - `remove`: Remove a key-value pair from the HashMap.
    - `contains_key`: Check if a key exists in the HashMap.
    - `clear`: Remove all key-value pairs from the HashMap.
    - `is_empty`: Check if the HashMap is empty.
    - `len`: Get the number of key-value pairs in the HashMap.
    - `capacity`: Get the capacity of the HashMap.

```rust
use std::collections::HashMap;

fn main() {
    let mut map: HashMap<u8, &str> = HashMap::new();

    let names = vec!["Deependu", "Nitesh", "Akash", "Bhim"];

    let mut roll = 1;
    for name in names {
        map.insert(roll, name);
        roll += 1;
    }

    // print hashmap
    println!("{map:?}");

    // get all the students
    while roll > 1 {
        roll -= 1;
        let student = map.get(&roll);
        if let Some(s) = student {
            println!("Roll: {roll} => {s}");
        }
    }

    let cap = map.capacity();
    let mut is_eppy = map.is_empty();
    let mut len = map.len();

    println!("{is_eppy}; {map:?}; {len}; {cap}");
    map.clear();
    is_eppy = map.is_empty();
    len = map.len();
    println!("{is_eppy}; {map:?}; {len}; {cap}");

    // map.entry(key). -- inplace modification of key corresponding entries
    let is_2_present = map.contains_key(&(2 as u8));
    let is_22_present = map.contains_key(&(22 as u8));
    println!("{is_2_present}; {is_22_present}");

    // -------- remove --------
    map.remove(&2);

    let is_2_present = map.contains_key(&(2 as u8));
    let is_22_present = map.contains_key(&(22 as u8));
    println!("{is_2_present}; {is_22_present}");
}
```

---

## In-Place modification of key entries

- `entry(key)`: Gets the given key's corresponding entry in the map for in-place manipulation.

!!! info "useful methods"
    - `or_insert(value)`: Inserts the given value if the key is not present in the map.
    - `or_insert_with(generator)`: Inserts the given value if the key is not present in the map.
    - `or_default()`: Inserts the default value if the key is not present in the map.
    - `and_modify(f)`: Inserts the given value if the key is not present in the map.

```rust
use std::collections::HashMap;

fn main() {
    let mut map: HashMap<i32, u32> = HashMap::new();

    // Ensures a value is in the entry by inserting the default value if empty
    map.entry(1).or_default();
    assert!(map.get(&1) == Some(&0));

    // Ensures a value is in the entry by inserting the default if empty
    map.entry(2).or_insert(1);
    assert!(map.get(&2) == Some(&1));

    // Provides in-place mutable access to an occupied entry before any potential inserts into the map.
    map.entry(3).and_modify(|e| *e += 1);
    assert!(map.get(&3) == None); // since `3` doesn't already exist in the map

    // if not already present, insert a value, else update the value.
    map.entry(3).and_modify(|e| *e += 1).or_insert(0);
    assert!(map.get(&3) == Some(&0)); // since `3` doesn't already exist in the map

    for _ in 0..3 {
        map.entry(3).and_modify(|e| *e += 1).or_insert(0);
    }
    assert!(map.get(&3) == Some(&3)); // since `3` doesn't already exist in the map

    // Ensures a value is in the entry by inserting the result of the default function if empty
    map.entry(4).or_insert_with(|| 3 * 2 + 5);
    assert!(map.get(&4) == Some(&11)); // since `3` doesn't already exist in the map

    // Ensures a value is in the entry by inserting,
    // This method allows for generating key-derived values for insertion by providing
    // the default function a reference to the key that was moved during the .entry(key) method call.
    map.entry(5).or_insert_with_key(|k| ((*k) * 2 + 7) as u32);
    assert!(map.get(&5) == Some(&17)); // since `3` doesn't already exist in the map
}
```

---

## Iterating over a HashMap

!!! info "Methods"
    - `iter`: Iterate over the key-value pairs in the map.
    - `iter_mut`: Iterate over the key-value pairs in the map, yielding mutable references.
    - `into_iter`: Iterate over the key-value pairs in the map, yielding owned values.
    - `iter().map()`: Apply a function to each key-value pair in the map.
    - `iter().filter()`: Filter the key-value pairs in the map.
    - `iter().for_each()`: Apply a function to each key-value pair in the map.
    - `iter().all()`: Check if all key-value pairs in the map satisfy a condition.
    - `iter().any()`: Check if any key-value pair in the map satisfies a condition.
    - `iter().collect()`: Collect the key-value pairs in the map into a vector.
    - `iter().enumerate()`: Iterate over the key-value pairs in the map, yielding the index and the key-value pair.

```rust
use std::collections::HashMap;

fn main() {
    let mut map = HashMap::new();

    for i in 1..11 {
        map.insert(i, i * 2);
    }

    // iterate
    for (k, v) in map.iter().enumerate() {
        println!("{k} => {v:?}")
    }

    // iter_mut
    for (_, v) in map.iter_mut() {
        *v *= 2;
    }

    let y = map.iter().all(|k_v| ((*(k_v.1)) % 2) == 0);
    map.iter().for_each(|zz| {
        let (_k, _v) = zz;
        // return (*_k) + (*_v);
        println!("{_k}; {_v}");
    });
    println!("{y}")
}
```
