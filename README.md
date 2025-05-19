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

### Multiple Spawn and Removing Drop

In this experiment, I tested what happens when:
1. We spawn multiple async tasks.
2. We remove the `drop(spawner)` line.

#### Multiple Spawn
![image](https://github.com/user-attachments/assets/9f94b8ef-b649-4ecc-8fd6-0f26dd6e7807)

Based on the output image above, we have each prints "howdyX", waits for 2 seconds, and prints "doneX". We also print an initial "hey hey". Changed code in `main.rs`:

```Rust
    ...
    spawner.spawn(async {
        println!("Andriyo Averill's Komputer: howdy2!");
        TimerFuture::new(Duration::new(2, 0)).await;
        println!("Andriyo Averill's Komputer: done2!");
    });

    spawner.spawn(async {
        println!("Andriyo Averill's Komputer: howdy3!");
        TimerFuture::new(Duration::new(2, 0)).await;
        println!("Andriyo Averill's Komputer: done3!");
    });
    
    println!("Andriyo Averill's Komputer: hey hey");
    ...
```

Turns out that the sequence of the "doneX" varies from output to output, it can outputs `done!` then `done2!`, and then `done3!` or it can also outputs like as shown in the output image above. This variation in the order of the `doneX!` messages is expected because of how asynchronous tasks and the executor operate. When we spawn all tasks start "at the same time" (asynchronously), so Async != in order. Just because we start 3 tasks in order doesn’t mean they finish in order — they finish when they're ready.

#### Removing Drop

![image](https://github.com/user-attachments/assets/dbc5aafb-d488-4b4b-aefc-707890a7b7cd)

Based on the output image above, removing the `drop(spawner` leads to the code running (hangs) forever. I had to manually stop it by `KEYBOARD_INTERRUPT` indicated by the **error: process didn't exit successfully: `target\debug\timer_future.exe` (exit code: 0xc000013a, STATUS_CONTROL_C_EXIT)** error message. Also, after running it several times, turns out the sequence of the `donex!` that appears stayed in order of the print statements are. So, it would look like 1,2,3 then 3,2,1 of the `howdy!` and `done!` respectively. This happens due to the executor blocks forever waiting for tasks that will never arrive. If we do have `drop(spawner)`, the machine basically says "No more tasks are coming. You can shut down after the queue is empty.".