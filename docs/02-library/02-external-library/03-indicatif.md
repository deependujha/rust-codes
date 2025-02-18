# **📌 Rust `indicatif` Cheat Sheet**  

The `indicatif` crate provides **progress bars, spinners, and multi-bar support** for terminal applications.

## **📦 Install `indicatif`**

```bash
cargo add indicatif
```

---

## **1️⃣ Basic Progress Bar**

```rust
use indicatif::ProgressBar;
use std::thread::sleep;
use std::time::Duration;

fn main() {
    let bar = ProgressBar::new(100); // Create a progress bar with 100 steps

    for _ in 0..100 {
        bar.inc(1); // Increment progress by 1
        sleep(Duration::from_millis(50));
    }

    bar.finish(); // Mark as completed
}
```

| Method | Description |
|--------|------------|
| `ProgressBar::new(n)` | Creates a new progress bar with `n` steps |
| `.inc(n)` | Increments progress by `n` |
| `.finish()` | Marks the bar as completed |

---

## **2️⃣ Customizing Progress Bar Style**

```rust
use indicatif::{ProgressBar, ProgressStyle};
use std::thread::sleep;
use std::time::Duration;

fn main() {
    let bar = ProgressBar::new(50);
    bar.set_style(
        ProgressStyle::default_bar()
            .template("{spinner:.green} [{elapsed_precise}] [{bar:40.cyan/blue}] {pos:>3}/{len} ({eta})")
            .unwrap()
            .progress_chars("#>-"),
    );

    for _ in 0..50 {
        bar.inc(1);
        sleep(Duration::from_millis(100));
    }

    bar.finish_with_message("✅ Done!");
}
```

| Method | Description |
|--------|------------|
| `.set_style(style)` | Sets a custom style for the progress bar |
| `.template("{...}")` | Defines the bar format |
| `.progress_chars("#>-")` | Sets progress characters |
| `.finish_with_message("text")` | Marks as finished with a message |

---

## **3️⃣ Using a Spinner**

```rust
use indicatif::{ProgressBar, ProgressStyle};
use std::thread::sleep;
use std::time::Duration;

fn main() {
    let spinner = ProgressBar::new_spinner();
    spinner.set_style(ProgressStyle::default_spinner().template("{spinner:.green} {msg}").unwrap());

    spinner.set_message("Loading...");
    
    for _ in 0..10 {
        spinner.tick(); // Update spinner animation
        sleep(Duration::from_millis(300));
    }

    spinner.finish_with_message("✅ Completed!");
}
```

| Method | Description |
|--------|------------|
| `ProgressBar::new_spinner()` | Creates a spinner |
| `.tick()` | Updates the spinner animation |
| `.set_message("text")` | Sets the spinner message |

---

## **4️⃣ Multi-Progress (Multiple Progress Bars)**

```rust
use indicatif::{MultiProgress, ProgressBar};
use std::thread;
use std::time::Duration;

fn main() {
    let m = MultiProgress::new();
    let bar1 = m.add(ProgressBar::new(50));
    let bar2 = m.add(ProgressBar::new(50));

    let handle1 = thread::spawn(move || {
        for _ in 0..50 {
            bar1.inc(1);
            thread::sleep(Duration::from_millis(50));
        }
        bar1.finish_with_message("✅ Task 1 Complete");
    });

    let handle2 = thread::spawn(move || {
        for _ in 0..50 {
            bar2.inc(1);
            thread::sleep(Duration::from_millis(70));
        }
        bar2.finish_with_message("✅ Task 2 Complete");
    });

    handle1.join().unwrap();
    handle2.join().unwrap();
}
```

| Method | Description |
|--------|------------|
| `MultiProgress::new()` | Creates a multi-progress handler |
| `.add(ProgressBar::new(n))` | Adds a new progress bar to multi-bar |

---

## **5️⃣ Download Progress with Bytes Format**

```rust
use indicatif::{ProgressBar, ProgressStyle};
use std::thread::sleep;
use std::time::Duration;

fn main() {
    let bar = ProgressBar::new(1024 * 1024 * 500); // 500 MB download
    bar.set_style(ProgressStyle::default_bar().template("{bar:40.cyan} {bytes}/{total_bytes} ({eta})").unwrap());

    for _ in 0..500 {
        bar.inc(1024 * 1024); // Simulating 1MB download per tick
        sleep(Duration::from_millis(100));
    }

    bar.finish_with_message("✅ Download Complete");
}
```

| Method | Description |
|--------|------------|
| `{bytes}` | Displays progress in bytes |
| `{total_bytes}` | Shows total bytes |
| `{eta}` | Shows estimated time remaining |

---

## **6️⃣ Steady Progress Bars (Auto-Increment)**

!!! example "why will I use it?"
    - You want a spinner or status indicator to keep moving even if no manual updates are made.
    - You're waiting for an event or long-running operation with unknown duration.
    - You need a "heartbeat" effect to indicate that a program is still alive.
    - Example Use Case: 🔹 Display a loading spinner while waiting for a network request.

```rust
use indicatif::{ProgressBar, ProgressStyle};
use std::thread::sleep;
use std::time::Duration;

fn main() {
    let spinner = ProgressBar::new_spinner();
    spinner.set_style(ProgressStyle::default_spinner().template("{spinner:.green} Waiting...").unwrap());

    spinner.enable_steady_tick(Duration::from_millis(100)); // Updates automatically every 100ms

    sleep(Duration::from_secs(5)); // Simulating a network request

    spinner.finish_with_message("✅ Request completed!");
}
```

| Method | Description |
|--------|------------|
| `.enable_steady_tick(duration)` | Automatically updates progress |

---

## **7️⃣ Creating Hidden Progress Bars**

```rust
use indicatif::ProgressBar;

fn main() {
    let bar = ProgressBar::hidden(); // Invisible bar
    bar.inc(1);
}
```

| Method | Description |
|--------|------------|
| `ProgressBar::hidden()` | Creates an invisible progress bar |

---

## **8️⃣ Using Progress Bar with Iterators**

```rust
use indicatif::ProgressIterator;
use std::thread::sleep;
use std::time::Duration;

fn main() {
    for _ in (0..100).progress() {
        sleep(Duration::from_millis(50));
    }
}
```

| Method | Description |
|--------|------------|
| `.progress()` | Adds a progress bar to an iterator |

---

## **📌 Summary**

| Feature | Method |
|---------|--------|
| **Basic Progress Bar** | `ProgressBar::new(n)`, `.inc(n)`, `.finish()` |
| **Custom Styles** | `.set_style(style)`, `.template("{bar}")` |
| **Spinner** | `ProgressBar::new_spinner()`, `.tick()` |
| **Multi-Progress** | `MultiProgress::new()`, `.add(bar)` |
| **Bytes Download** | `{bytes}/{total_bytes}`, `{eta}` |
| **Steady Tick** | `.enable_steady_tick(duration)` |
| **Hidden Bar** | `ProgressBar::hidden()` |
| **Iterator Integration** | `.progress()` |
