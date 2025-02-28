# Rust String Methods

Rust's `String` and `&str` types provide many useful methods similar to Python's string methods. Below is a list of common operations and their equivalents in Rust.

## Checking Prefix & Suffix

  ```rust
  let s = "hello world";
  assert!(s.starts_with("hello"));
  assert!(s.ends_with("world"));
  assert!(s.contains("lo"));
  ```

## Trimming Whitespace

  ```rust
  let s = "  hello  ";
  assert_eq!(s.trim(), "hello");
  assert_eq!(s.trim_start(), "hello  ");
  assert_eq!(s.trim_end(), "  hello");
  ```

## Modifying Case

  ```rust
  let s = "Hello";
  assert_eq!(s.to_lowercase(), "hello");
  assert_eq!(s.to_uppercase(), "HELLO");
  ```

## Splitting & Joining

- **`split`**: Splits a string on a delimiter.

  ```rust
  let s = "a,b,c";
  let parts: Vec<&str> = s.split(',').collect();
  assert_eq!(parts, vec!["a", "b", "c"]);
  ```

- **`split_whitespace`**: Splits by whitespace.

  ```rust
  let s = "hello   world";
  let parts: Vec<&str> = s.split_whitespace().collect();
  assert_eq!(parts, vec!["hello", "world"]);
  ```

  **`splitn`**: Splits a string into a maximum of N substrings.

  ```rust
  let s = "a,b,c,d";
  let parts: Vec<&str> = s.splitn(2, ',').collect();
  assert_eq!(parts, vec!["a", "b,c,d"]);
  ```

- **`join`**: Joins an iterator of strings with a separator.

  ```rust
  let words = vec!["hello", "world"];
  let s = words.join(" ");
  assert_eq!(s, "hello world");
  ```

## Replacing & Removing Characters

- **`replace`**: Replaces all occurrences of a substring.

  ```rust
  let s = "hello world";
  assert_eq!(s.replace("world", "Rust"), "hello Rust");
  ```

- **`replacen`**: Replaces the first N occurrences.

  ```rust
  let s = "hello world world";
  assert_eq!(s.replacen("world", "Rust", 1), "hello Rust world");
  ```

- **`trim_matches`**: Trims specific characters from start and end.

  ```rust
  let s = "---hello---";
  assert_eq!(s.trim_matches('-'), "hello");
  ```

- **`trim_start_matches`**: Trims specific characters from start.

  ```rust
  let s = "s3://hello";
  assert_eq!(s.trim_start_matches("s3://"), "hello");
  ```

## Indexing & Slicing

- **`chars`**: Iterates over characters.

  ```rust
  let s = "hello";
  for c in s.chars() {
      println!("{}", c);
  }
  ```

- **`bytes`**: Iterates over bytes.

  ```rust
  let s = "hello";
  for b in s.bytes() {
      println!("{}", b);
  }
  ```

- **Slicing**: Getting a substring using byte indices.

  ```rust
  let s = "hello world";
  let sub = &s[0..5];
  assert_eq!(sub, "hello");
  ```

  **⚠ Note**: Rust strings are UTF-8 encoded, so slicing must align with character boundaries.

## Checking Empty or Length

- **`is_empty`**: Checks if a string is empty.

  ```rust
  let s = "";
  assert!(s.is_empty());
  ```

- **`len`**: Gets the length (in bytes, not characters!).
  
  ```rust
  let s = "hello";
  assert_eq!(s.len(), 5);
  ```

## Converting Between Strings

- **`to_string`**: Converts into a `String`.
  
  ```rust
  let s = "hello".to_string();
  ```

- **`String::from`**: Creates a `String` from a `&str`.

  ```rust
  let s = String::from("hello");
  ```
