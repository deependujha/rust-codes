# Futures Notes

## 1. Introduction to Futures

The `futures` crate provides abstractions for asynchronous programming in Rust. It extends the standard library's `Future` trait with additional combinators, utilities, and runtime-independent async features.

## 2. Adding Futures to Cargo.toml

```toml
[dependencies]
futures = "0.3"
```

## 3. Basic Future Example

```rust
use futures::executor::block_on;

async fn hello() {
    println!("Hello, Future!");
}

fn main() {
    block_on(hello());
}
```

## 4. Using `Future::poll`

```rust
use futures::future::Future;
use std::pin::Pin;
use std::task::{Context, Poll};

struct MyFuture;

impl Future for MyFuture {
    type Output = i32;
    
    fn poll(self: Pin<&mut Self>, _cx: &mut Context<'_>) -> Poll<Self::Output> {
        Poll::Ready(42)
    }
}

fn main() {
    let mut future = MyFuture;
    let waker = futures::task::noop_waker();
    let mut context = Context::from_waker(&waker);
    
    match Pin::new(&mut future).poll(&mut context) {
        Poll::Ready(value) => println!("Future ready with value: {}", value),
        Poll::Pending => println!("Future pending"),
    }
}
```

## 5. Combining Futures with `join!`

```rust
use futures::join;

async fn async_task1() -> i32 {
    1
}
async fn async_task2() -> i32 {
    2
}

#[tokio::main]
async fn main() {
    let (a, b) = join!(async_task1(), async_task2());
    println!("Results: {}, {}", a, b);
}
```

## 6. Chaining with `then` and `map`

```rust
use futures::future::ready;
use futures::FutureExt;

#[tokio::main]
async fn main() {
    let fut = ready(10).map(|x| x * 2);
    println!("Result: {}", fut.await);
}
```

## 7. Using `select!` for Race Conditions

```rust
use futures::future::{ready, pending};
use futures::select;

#[tokio::main]
async fn main() {
    let a = ready("Task A done");
    let b = pending::<&str>();
    
    select! {
        result = a => println!("First completed: {}", result),
        result = b => println!("Second completed: {}", result),
    }
}
```

## 8. Streams with `futures::stream`

```rust
use futures::stream::{self, StreamExt};

#[tokio::main]
async fn main() {
    let mut stream = stream::iter(vec![1, 2, 3]);
    
    while let Some(value) = stream.next().await {
        println!("Received: {}", value);
    }
}
```

## 9. Buffered Concurrency with `buffer_unordered`

```rust
use futures::stream::{self, StreamExt};
use futures::future;

#[tokio::main]
async fn main() {
    let tasks = stream::iter(vec![1, 2, 3])
        .map(|num| async move {
            println!("Processing {}", num);
            num * 2
        })
        .buffer_unordered(2);

    tasks.for_each(|result| async move {
        println!("Completed: {}", result);
    }).await;
}
```

## 10. Using `futures::channel::mpsc`

```rust
use futures::channel::mpsc;
use futures::sink::SinkExt;
use futures::stream::StreamExt;

#[tokio::main]
async fn main() {
    let (mut tx, mut rx) = mpsc::channel(10);

    tokio::spawn(async move {
        tx.send("Message").await.unwrap();
    });

    while let Some(msg) = rx.next().await {
        println!("Received: {}", msg);
    }
}
```
