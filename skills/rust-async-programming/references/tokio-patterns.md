# Tokio Patterns

## Runtime Configuration

```rust
// Multi-threaded (default with #[tokio::main])
let rt = tokio::runtime::Builder::new_multi_thread()
    .worker_threads(4)        // default: num CPUs
    .max_blocking_threads(512) // for blocking I/O
    .enable_all()
    .thread_name("my-worker")
    .build()
    .unwrap();

// Current-thread (single-threaded, cooperative)
let rt = tokio::runtime::Builder::new_current_thread()
    .enable_all()
    .build()
    .unwrap();
```

## Spawning Tasks

```rust
// Spawn on the runtime (requires 'static + Send)
let handle = tokio::spawn(async move {
    compute().await
});
let result = handle.await.unwrap();

// Spawn blocking (for CPU-intensive or sync code)
let result = tokio::task::spawn_blocking(|| {
    heavy_computation()  // runs on blocking thread pool
}).await.unwrap();

// Spawn local (not Send, current-thread only)
tokio::task::spawn_local(async {
    // can use !Send types here
});
```

## JoinSet: Managing Multiple Tasks

```rust
use tokio::task::JoinSet;

let mut set = JoinSet::new();
for url in urls {
    set.spawn(async move { fetch(url).await });
}

// Collect results as they complete
let mut results = Vec::new();
while let Some(res) = set.join_next().await {
    results.push(res.unwrap());
}

// Abort all remaining
set.abort_all();
```

## Channels

### mpsc (Multi-producer, single-consumer)

```rust
let (tx, mut rx) = tokio::sync::mpsc::channel::<Message>(100);

// Producer
let tx2 = tx.clone();
tokio::spawn(async move {
    tx2.send(Message::Ping).await.unwrap();
});

// Consumer
while let Some(msg) = rx.recv().await {
    handle(msg);
}
```

### oneshot (Single value)

```rust
let (tx, rx) = tokio::sync::oneshot::channel();
tokio::spawn(async move {
    let result = compute().await;
    tx.send(result).unwrap();
});
let value = rx.await.unwrap();
```

### broadcast (Multi-consumer)

```rust
let (tx, _) = tokio::sync::broadcast::channel(16);
let mut rx1 = tx.subscribe();
let mut rx2 = tx.subscribe();

tx.send("hello").unwrap();
// Both rx1 and rx2 receive "hello"
```

### watch (Single value, notifies on change)

```rust
let (tx, mut rx) = tokio::sync::watch::channel(Config::default());

// Sender updates the value
tx.send(Config::new()).unwrap();

// Receiver waits for changes
rx.changed().await.unwrap();
let config = rx.borrow().clone();
```

## Timeouts and Intervals

```rust
use tokio::time::{timeout, sleep, interval, Duration};

// Timeout
match timeout(Duration::from_secs(5), operation()).await {
    Ok(result) => result,
    Err(_) => return Err(Error::Timeout),
}

// Sleep
sleep(Duration::from_millis(100)).await;

// Interval (periodic)
let mut interval = interval(Duration::from_secs(1));
loop {
    interval.tick().await;
    do_periodic_work().await;
}
```

## Graceful Shutdown

```rust
use tokio::signal;
use tokio::sync::broadcast;

#[tokio::main]
async fn main() {
    let (shutdown_tx, _) = broadcast::channel(1);

    let server = tokio::spawn(run_server(shutdown_tx.subscribe()));
    let worker = tokio::spawn(run_worker(shutdown_tx.subscribe()));

    // Wait for Ctrl+C
    signal::ctrl_c().await.unwrap();
    println!("Shutting down...");
    drop(shutdown_tx);  // all receivers get error → they exit

    let _ = tokio::join!(server, worker);
}

async fn run_server(mut shutdown: broadcast::Receiver<()>) {
    loop {
        tokio::select! {
            conn = accept_connection() => handle(conn),
            _ = shutdown.recv() => break,
        }
    }
}
```

## Semaphore (Concurrency Limiting)

```rust
use tokio::sync::Semaphore;
use std::sync::Arc;

let semaphore = Arc::new(Semaphore::new(10));  // max 10 concurrent

for url in urls {
    let permit = semaphore.clone().acquire_owned().await.unwrap();
    tokio::spawn(async move {
        let _permit = permit;  // held until dropped
        fetch(url).await;
    });
}
```

## select! Patterns

```rust
use tokio::sync::mpsc;

let mut rx1 = get_receiver_1();
let mut rx2 = get_receiver_2();

loop {
    tokio::select! {
        Some(msg) = rx1.recv() => handle_1(msg),
        Some(msg) = rx2.recv() => handle_2(msg),
        else => break,  // both channels closed
    }
}
```

## Error Handling in Tasks

```rust
let handle = tokio::spawn(async {
    might_panic().await
});

match handle.await {
    Ok(result) => println!("success: {result:?}"),
    Err(e) if e.is_panic() => println!("task panicked!"),
    Err(e) if e.is_cancelled() => println!("task was cancelled"),
    Err(e) => println!("other error: {e}"),
}
```
