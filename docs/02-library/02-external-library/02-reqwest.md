# **📌 Rust `reqwest` Cheat Sheet**  

**`reqwest`** is a popular HTTP client for Rust, built on top of `hyper`. It supports **async** and **blocking** requests.

## **📦 Add `reqwest` to `Cargo.toml`**

```toml
[dependencies]
reqwest = { version = "0.11", features = ["json", "blocking", "stream"] }
tokio = { version = "1", features = ["full"] } # Needed for async
serde = { version = "1", features = ["derive"] } # Needed for JSON serialization
```

---

## **1️⃣ Making a Basic GET Request**

```rust
use reqwest;

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let response = reqwest::get("https://jsonplaceholder.typicode.com/posts/1")
        .await?
        .text()
        .await?;

    println!("Response: {}", response);
    Ok(())
}
```

| Method | Description |
|--------|------------|
| `.get(url).await?` | Makes a **GET** request |
| `.text().await?` | Gets the response body as a **String** |
| `.json::<T>().await?` | Parses JSON into a struct |

---

## **2️⃣ Using a `Client` for Multiple Requests**

```rust
use reqwest::Client;

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let client = Client::new(); // Create once, reuse

    let res1 = client.get("https://jsonplaceholder.typicode.com/posts/1")
        .send()
        .await?
        .text()
        .await?;

    let res2 = client.get("https://jsonplaceholder.typicode.com/posts/2")
        .send()
        .await?
        .text()
        .await?;

    println!("Response 1: {}", res1);
    println!("Response 2: {}", res2);

    Ok(())
}
```

**Why use a `Client`?**  
✔️ **Efficient**: Avoids re-establishing connections  
✔️ **Reuses TCP connections**  
✔️ **Allows setting default headers, timeout, etc.**

---

## **3️⃣ Sending a POST Request with JSON**

```rust
use reqwest::Client;
use serde_json::json;

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let client = Client::new();

    let response = client.post("https://jsonplaceholder.typicode.com/posts")
        .json(&json!({
            "title": "Hello",
            "body": "This is a test post",
            "userId": 1
        }))
        .send()
        .await?
        .text()
        .await?;

    println!("Response: {}", response);
    Ok(())
}
```

| Method | Description |
|--------|------------|
| `.post(url)` | Creates a **POST** request |
| `.json(&data)` | Sends **JSON data** (requires `serde_json`) |

---

## **4️⃣ PUT Request (Updating Data)**

```rust
use reqwest::Client;
use serde_json::json;

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let client = Client::new();

    let response = client.put("https://jsonplaceholder.typicode.com/posts/1")
        .json(&json!({
            "id": 1,
            "title": "Updated Title",
            "body": "Updated body content",
            "userId": 1
        }))
        .send()
        .await?
        .text()
        .await?;

    println!("Response: {}", response);
    Ok(())
}
```

| Method | Description |
|--------|------------|
| `.put(url)` | Makes a **PUT** request (update resource) |

---

## **5️⃣ DELETE Request**

```rust
use reqwest::Client;

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let client = Client::new();

    let response = client.delete("https://jsonplaceholder.typicode.com/posts/1")
        .send()
        .await?
        .text()
        .await?;

    println!("Deleted Response: {}", response);
    Ok(())
}
```

| Method | Description |
|--------|------------|
| `.delete(url)` | Deletes a resource |

---

## **6️⃣ Setting Headers**

```rust
use reqwest::{Client, header};

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let client = Client::new();

    let response = client.get("https://jsonplaceholder.typicode.com/posts/1")
        .header(header::USER_AGENT, "MyRustApp/1.0")
        .send()
        .await?
        .text()
        .await?;

    println!("Response: {}", response);
    Ok(())
}
```

| Method | Description |
|--------|------------|
| `.header(header::NAME, "Value")` | Adds custom headers |

---

## **7️⃣ Streaming Large Downloads**

```rust
use reqwest::Client;
use tokio::io::AsyncWriteExt; // For writing to file

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let client = Client::new();

    let mut response = client.get("https://speed.hetzner.de/100MB.bin")
        .send()
        .await?
        .bytes_stream();

    let mut file = tokio::fs::File::create("downloaded_file.bin").await?;

    while let Some(chunk) = response.next().await {
        file.write_all(&chunk?).await?;
    }

    println!("Download complete!");
    Ok(())
}
```

| Method | Description |
|--------|------------|
| `.bytes_stream()` | Streams the response body |
| `.write_all(&chunk?)` | Writes each chunk to a file |

---

## **8️⃣ Handling Timeouts**

```rust
use reqwest::Client;
use std::time::Duration;

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let client = Client::builder()
        .timeout(Duration::from_secs(5)) // Set timeout
        .build()?;

    let response = client.get("https://jsonplaceholder.typicode.com/posts/1")
        .send()
        .await?
        .text()
        .await?;

    println!("Response: {}", response);
    Ok(())
}
```

| Method | Description |
|--------|------------|
| `.timeout(Duration::from_secs(n))` | Sets a request timeout |

---

## **9️⃣ Handling Errors Gracefully**

```rust
use reqwest::Client;

#[tokio::main]
async fn main() {
    let client = Client::new();
    
    match client.get("https://invalid-url.typicode.com")
        .send()
        .await
    {
        Ok(response) => println!("Response: {:?}", response),
        Err(err) => println!("Error: {}", err),
    }
}
```

| Method | Description |
|--------|------------|
| `.send().await?` | Returns a `Result<Response, reqwest::Error>` |

---

## **🔟 Blocking Requests (Sync)**

For **synchronous (blocking) requests**, use:

```rust
use reqwest::blocking::get;

fn main() -> Result<(), reqwest::Error> {
    let response = get("https://jsonplaceholder.typicode.com/posts/1")?
        .text()?;
    
    println!("Response: {}", response);
    Ok(())
}
```

| Feature | Description |
|---------|------------|
| `"blocking"` | Enables **synchronous** requests |
| `reqwest::blocking::get()` | Sync version of `reqwest::get()` |

---

## **📌 Summary**

| Category | Method |
|----------|--------|
| **Basic Requests** | `.get()`, `.post()`, `.put()`, `.delete()` |
| **Sending Data** | `.json(&data)`, `.header()` |
| **Streaming Data** | `.bytes_stream()`, `.write_all()` |
| **Handling Errors** | `match response.send()` |
| **Blocking Mode** | `reqwest::blocking::get()` |
| **Timeouts** | `.timeout(Duration::from_secs(n))` |
