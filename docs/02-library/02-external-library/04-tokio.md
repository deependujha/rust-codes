# Tokio Notes

## 1. Introduction to Tokio

Tokio is an asynchronous runtime for Rust, designed for building fast and reliable network applications. It provides async/await support, efficient task scheduling, and various utilities for working with async operations.

## 2. Setting Up Tokio

To use Tokio in your Rust project, add the following to `Cargo.toml`:

```toml
[dependencies]
tokio = { version = "1", features = ["full"] }
```

## 3. Basic Async Runtime

A Tokio async main function must be annotated with `#[tokio::main]`:

```rust
#[tokio::main]
async fn main() {
    println!("Hello from Tokio!");
}
```

Alternatively, you can manually create a runtime:

```rust
fn main() {
    let rt = tokio::runtime::Runtime::new().unwrap();
    rt.block_on(async {
        println!("Manually managed runtime");
    });
}
```

## 4. Spawning Async Tasks

```rust
#[tokio::main]
async fn main() {
    tokio::spawn(async {
        println!("Running in the background!");
    });
}
```

## 5. Async Sleep

```rust
use tokio::time::{sleep, Duration};

#[tokio::main]
async fn main() {
    println!("Sleeping for 2 seconds...");
    sleep(Duration::from_secs(2)).await;
    println!("Awake now!");
}
```

## 6. Using Tokio Channels

Tokio provides async channels for communication between tasks:

```rust
use tokio::sync::mpsc;

#[tokio::main]
async fn main() {
    let (tx, mut rx) = mpsc::channel(32);

    tokio::spawn(async move {
        tx.send("Hello from task").await.unwrap();
    });

    let msg = rx.recv().await.unwrap();
    println!("Received: {msg}");
}
```

## 7. Handling Concurrency with Join

```rust
async fn task1() {
    println!("Task 1");
}
async fn task2() {
    println!("Task 2");
}

#[tokio::main]
async fn main() {
    tokio::join!(task1(), task2());
}
```

## 8. Using Tokio Mutex

```rust
use std::sync::Arc;
use tokio::sync::Mutex;

#[tokio::main]
async fn main() {
    let data = Arc::new(Mutex::new(0));

    let data_cloned = data.clone();
    tokio::spawn(async move {
        let mut num = data_cloned.lock().await;
        *num += 1;
    }).await.unwrap();

    println!("Final value: {}", *data.lock().await);
}
```

## 9. Streaming with Tokio

```rust
use tokio::sync::mpsc;
use tokio_stream::StreamExt;

#[tokio::main]
async fn main() {
    let (tx, rx) = mpsc::channel(32);
    let mut stream = tokio_stream::wrappers::ReceiverStream::new(rx);

    tokio::spawn(async move {
        tx.send(42).await.unwrap();
    });

    while let Some(value) = stream.next().await {
        println!("Received: {value}");
    }
}
```

## 10. Using Tokio with HTTP (reqwest)

```rust
use reqwest;

#[tokio::main]
async fn main() {
    let resp = reqwest::get("https://api.github.com/repos/tokio-rs/tokio")
        .await
        .unwrap()
        .text()
        .await
        .unwrap();
    println!("Response: {resp}");
}
```

## 11. Using Tokio Runtime Without `#[tokio::main]`

```rust
use tokio::runtime::Runtime;

fn main() {
    let rt = Runtime::new().unwrap();
    rt.block_on(async {
        println!("Using Tokio without #[tokio::main]");
    });
}
```

## 12. Tokio File IO with Async Read/Write

```rust
use tokio::fs::File;
use tokio::io::{AsyncReadExt, AsyncWriteExt};

#[tokio::main]
async fn main() -> std::io::Result<()> {
    let mut file = File::create("test.txt").await?;
    file.write_all(b"Hello, Tokio!").await?;

    let mut file = File::open("test.txt").await?;
    let mut contents = vec![];
    file.read_to_end(&mut contents).await?;
    println!("File contents: {:?}", String::from_utf8_lossy(&contents));

    Ok(())
}
```

## 13. Tokio Tasks with Timeouts

```rust
use tokio::time::{timeout, Duration};

#[tokio::main]
async fn main() {
    let result = timeout(Duration::from_secs(2), async {
        tokio::time::sleep(Duration::from_secs(3)).await;
        "Completed"
    }).await;

    match result {
        Ok(val) => println!("Task finished: {val}"),
        Err(_) => println!("Task timed out"),
    }
}
```

## 14. Tokio Signal Handling

```rust
use tokio::signal;

#[tokio::main]
async fn main() {
    println!("Press Ctrl+C to exit");
    signal::ctrl_c().await.unwrap();
    println!("Shutting down");
}
```

## 15. Tokio Thread Pool

```rust
use tokio::runtime::Builder;

fn main() {
    let rt = Builder::new_multi_thread()
        .worker_threads(4)
        .enable_all()
        .build()
        .unwrap();
    rt.block_on(async {
        println!("Using a multi-threaded Tokio runtime");
    });
}
```
