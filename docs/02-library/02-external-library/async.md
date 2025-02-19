In **async Rust**, the execution of asynchronous tasks is driven by the **`Future`** trait, which is at the core of Rust's async model. Here’s a **complete and corrected** explanation:

---

### **The `Future` Trait**
The `Future` trait represents a computation that **might not have completed yet** but will produce a value at some point. It is defined as:

```rust
trait Future {
    type Output;
    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output>;
}
```

### **How `poll` Works**
- The **poll method** is the key mechanism in Rust's async system.
- It is **not** called automatically; instead, an **executor** calls it.
- `poll` takes a **pinned mutable reference** (`Pin<&mut Self>`) to the future.
- It also takes a **context** (`&mut Context<'_>`), which provides a way to register **wakers**.

### **Return Values of `poll`**
`poll` returns a `Poll<T>`, which can be:
1. **`Poll::Pending`** → The future is **not ready yet**, but the executor should call `poll` again later.
2. **`Poll::Ready(output)`** → The future is **done**, and it returns the computed value.

### **Who Calls `poll`?**
- Futures **do not start automatically** when created.
- They are **lazy**, meaning `poll` is only called when:
  - The future is **awaited**.
  - The future is **polled** manually by an executor.

### **Wakers and `Context`**
When a future returns `Poll::Pending`, it must register a **waker** inside `Context`. The waker tells the executor **when the future is ready to be polled again**.

### **How `async` Functions Work**
When you write an `async fn`, the Rust compiler:
1. Transforms it into a **state machine** that implements `Future`.
2. Calls `poll` whenever needed until completion.

---

### **Example: Implementing a Custom Future**
Here’s a simple example of implementing a **manual Future**:

```rust
use std::{
    pin::Pin,
    task::{Context, Poll},
    time::Instant,
};
use tokio::time::sleep;
use std::future::Future;

struct Delay {
    when: Instant,
}

impl Future for Delay {
    type Output = ();

    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        if Instant::now() >= self.when {
            Poll::Ready(())
        } else {
            cx.waker().wake_by_ref(); // Re-schedule the future
            Poll::Pending
        }
    }
}
```

---

### **Summary**
- `Future` is a trait with a `poll` method.
- `poll` is called by the executor, **not** automatically.
- It returns `Poll::Pending` (if not done) or `Poll::Ready(value)`.
- If `Pending`, a **waker** must be registered to resume execution later.
- `async` functions are **state machines** that implement `Future` automatically.

Let me know if you want a deeper breakdown of any part! 🚀