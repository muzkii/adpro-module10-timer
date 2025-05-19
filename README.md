# Advanced Programming Rust 
by Andriyo Averill Fahrezi, NPM of 2306172325

## Module 10 - Asynchronous Programming - Timer

### Understanding How It Works
![Screenshot 2025-05-19 154145](https://github.com/user-attachments/assets/9567c3f1-539a-4b10-947b-ccb5cce740ec)

In this experiment, as seen on the output image above, I added a print statement right after spawning the async task with:
```rust
println!("Andriyo Averill's Komputer: hey hey");
```
Even though this line comes after the `spawner.spawn(...)`, it prints before the howdy! from the spawned async task.

This is because spawning only schedules the `task;` it doesn’t run it immediately. The async task begins execution only after the `executor.run()` is called. Therefore, synchronous code like `println!("hey hey")` runs first, before the executor starts polling tasks.
