# 🕒 Time in Rust

## ✅ From the Standard Library (`std::time`)

```rust
use std::time::{SystemTime, UNIX_EPOCH, Duration};
```

---

### ⏰ Get current system time (as UNIX timestamp)

```rust
let now = SystemTime::now();
let since_epoch = now.duration_since(UNIX_EPOCH)?;
println!("Seconds since epoch: {}", since_epoch.as_secs());
```

---

### 🕑 Measure elapsed time

```rust
let start = std::time::Instant::now();

// some computation
std::thread::sleep(Duration::from_secs(2));

let duration = start.elapsed();
println!("Time elapsed: {:?}", duration);
```

---

## 🧭 Using `chrono` crate (Recommended for more flexibility)

Add to `Cargo.toml`:

```toml
chrono = "0.4"
```

```rust
use chrono::{Local, Utc, Datelike, Timelike};
```

---

### 📆 Get current date/time

```rust
let now = Local::now(); // or Utc::now()
println!("Local: {}", now);
```

### 🧩 Access parts

```rust
println!(
    "{}-{:02}-{:02} {}:{:02}:{:02}",
    now.year(),
    now.month(),
    now.day(),
    now.hour(),
    now.minute(),
    now.second(),
);
```

---

### 📅 Parse and format dates

```rust
use chrono::NaiveDateTime;

let dt = NaiveDateTime::parse_from_str("2025-06-01 14:30:00", "%Y-%m-%d %H:%M:%S")?;
println!("Parsed datetime: {}", dt);

let formatted = now.format("%Y-%m-%d %H:%M:%S").to_string();
println!("Formatted: {}", formatted);
```

---

## ✅ Summary Table

| Task                 | `std::time`                | `chrono`                       |
| -------------------- | -------------------------- | ------------------------------ |
| Get current time     | `SystemTime::now()`        | `Local::now()` / `Utc::now()`  |
| Time since epoch     | `duration_since(...)`      | `timestamp()`                  |
| Measure elapsed time | `Instant::now().elapsed()` | `Utc::now() - other_time`      |
| Formatting / Parsing | ❌ (Manual only)            | ✅ Built-in with `format()`     |
| Date math (add/sub)  | Manual with `Duration`     | Easy with `+/-` and `Duration` |
