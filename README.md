# Module 10 – Asynchronous Programming
**Andhika Nayaka Arya Wibowo · 2306174135**

---

## 1.2 · Understanding How It Works

### Screenshot

![Output of the program showing the main-thread line printed first, followed by async task lines](img/ss1.png)

### Code Modification

In this experiment, I inserted a `println!` statement immediately after calling `spawner.spawn(...)` to help observe how asynchronous tasks are scheduled and executed. Here's a simplified version of the code snippet:

```rust
spawner.spawn(async {
    println!("async task: howdy!");
    TimerFuture::new(Duration::new(2, 0)).await;
    println!("async task: done!");
});

// Diagnostic println! to observe order
println!("Nayaka's Komputer: hey hey");
```

### What I Observed

When I ran the program, the output printed:

```
Nayaka's Komputer: hey hey
Nayaka's Komputer: howdy!
Nayaka's Komputer: done!
```

This shows that the line **after** the `spawn` call (`"hey hey"`) executed first—even though it was written after the async block in the code.

| Console Order | Output Line                        | Explanation                                                                 |
|---------------|-------------------------------------|-----------------------------------------------------------------------------|
| 1             | `Nayaka's Komputer: hey hey`       | `spawn` is non-blocking, so this line runs immediately on the main thread. |
| 2             | `Nayaka's Komputer: howdy!`        | The executor starts polling the future, and the async block begins.        |
| 3             | `Nayaka's Komputer: done!`         | After `TimerFuture` finishes sleeping, the future resumes and completes.   |

This experiment demonstrates Rust's asynchronous behavior clearly:

- **`spawn()` queues the task**, but does not execute it immediately.
- **Execution continues on the main thread**, meaning anything after `spawn()` runs without delay.
- The **executor polls the future** separately, allowing it to proceed independently.

This shows the non-blocking nature of Rust’s async model: futures are lazy and need to be driven by an executor, making them efficient for handling many I/O-bound tasks without wasting CPU.