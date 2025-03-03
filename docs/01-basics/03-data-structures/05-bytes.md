# Bytes

- `Vec<u8>` or `&[u8]` represents raw bytes in Rust
- Common in file I/O, networking, and binary data handling
- `std::io` provides traits for reading and writing bytes

---

## Basic Byte Operations

### Creating Byte Collections

```rust
// From string
let bytes = "Hello".as_bytes();
let byte_vec = Vec::from("Hello".as_bytes());

// Manual creation
let bytes = vec![72, 101, 108, 108, 111];  // "Hello"

// From hex
let bytes = vec![0xFF, 0x00, 0xAB];
```

### Converting Bytes

```rust
fn main() {
    let bytes = "Hello".as_bytes();
    
    // Bytes to string (safe)
    let text = String::from_utf8_lossy(bytes);
    
    // Bytes to string (when sure it's valid UTF-8)
    if let Ok(text) = String::from_utf8(bytes.to_vec()) {
        println!("{text}");
    }
    
    // Single byte to char (only works for ASCII)
    let c = bytes[0] as char;
}
```

---

## File Operations

### Reading Files

```rust
use std::fs::File;
use std::io::{self, Read, Seek, SeekFrom};

fn main() -> io::Result<()> {
    // Read entire file into bytes
    let bytes = std::fs::read("file.txt")?;
    
    // Read with buffer
    let mut file = File::open("file.txt")?;
    let mut buffer = Vec::new();
    file.read_to_end(&mut buffer)?;
    
    // Read fixed chunks
    let mut buffer = [0; 1024];  // 1KB buffer
    let n = file.read(&mut buffer)?;
    println!("Read {n} bytes");
    
    Ok(())
}
```

### Writing Files

```rust
use std::fs::File;
use std::io::Write;

fn main() -> io::Result<()> {
    // Write bytes to file
    std::fs::write("output.bin", &[0xFF, 0x00, 0xAB])?;
    
    // Append to file
    let mut file = std::fs::OpenOptions::new()
        .append(true)
        .open("output.bin")?;
    file.write_all(&[0xCD, 0xEF])?;
    
    Ok(())
}
```

### Seeking in Files

```rust
use std::fs::File;
use std::io::{self, Seek, SeekFrom, Read};

fn main() -> io::Result<()> {
    let mut file = File::open("file.bin")?;
    
    // Seek from start
    file.seek(SeekFrom::Start(10))?;
    
    // Seek from current position
    file.seek(SeekFrom::Current(5))?;
    
    // Seek from end
    file.seek(SeekFrom::End(-5))?;
    
    // Read after seeking
    let mut buffer = [0; 10];
    file.read_exact(&mut buffer)?;
    
    Ok(())
}
```

---

## BufReader and BufWriter

`BufReader` and `BufWriter` provide buffered I/O operations, which can significantly improve performance when:

- Reading/writing small amounts of data frequently
- Working with large files
- Dealing with slow devices (like network or disk)

### Why Use Them?

- Reduces system calls by buffering data in memory
- `BufReader`: Instead of reading one byte at a time, it reads chunks into a buffer
- `BufWriter`: Collects small writes into larger chunks before writing to disk
- Can improve performance by 10x or more in some cases

```rust
use std::io::{BufReader, BufWriter, Read, Write};
use std::fs::File;

fn main() -> io::Result<()> {
    // Buffered reading
    let file = File::open("large.txt")?;
    let mut reader = BufReader::new(file);
    let mut buffer = String::new();
    reader.read_to_string(&mut buffer)?;
    
    // Buffered writing
    let file = File::create("output.txt")?;
    let mut writer = BufWriter::new(file);
    writer.write_all(b"Hello World")?;
    writer.flush()?;  // Don't forget to flush!
    
    Ok(())
}
```

### Reading a file `line by line`

```rust
use std::fs::File;
use std::io::{self, BufRead};

fn main() -> io::Result<()> {
    let file = File::open("large.txt")?; // Open the file
    let reader = io::BufReader::new(file); // Wrap in BufReader

    for line in reader.lines() {
        let line = line?; // Handle potential errors
        println!("{}", line);
    }

    Ok(())
}
```

!!! tip "Remember"
    - Always handle errors with `?` or `unwrap()`/`expect()`
    - Use buffered readers/writers for large files
    - Close files implicitly with scope or explicitly with `drop()`
    - Use `flush()` when writing to ensure all data is written
