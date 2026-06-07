# Futures, Pin/Unpin, Executors & Wakers

## The Future Trait In Depth

```rust
pub trait Future {
    type Output;
    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output>;
}

pub enum Poll<T> {
    Ready(T),
    Pending,
}
```

Key contract:
- Return `Poll::Pending` → MUST arrange for `cx.waker().wake()` to be called later
- After returning `Poll::Ready` → must not be polled again (fuse if needed)

## Building a Simple Executor

```rust
use std::future::Future;
use std::pin::Pin;
use std::sync::{Arc, Mutex};
use std::task::{Context, Poll, Wake, Waker};
use std::collections::VecDeque;

struct SimpleExecutor {
    queue: VecDeque<Pin<Box<dyn Future<Output = ()>>>>,
}

impl SimpleExecutor {
    fn run(&mut self) {
        while let Some(mut task) = self.queue.pop_front() {
            let waker = Waker::noop();
            let mut cx = Context::from_waker(&waker);
            match task.as_mut().poll(&mut cx) {
                Poll::Ready(()) => {} // task done
                Poll::Pending => self.queue.push_back(task), // re-queue
            }
        }
    }
}
```

## How Wakers Work

The `Waker` is the mechanism for I/O readiness notification:

```rust
// Waker is backed by a RawWaker with a vtable
pub struct Waker { /* ... */ }

impl Waker {
    pub fn wake(self) { /* signal executor to re-poll this task */ }
    pub fn wake_by_ref(&self) { /* same, without consuming */ }
}
```

### Custom Waker (Thread Parker)

```rust
use std::task::{RawWaker, RawWakerVTable, Waker};
use std::thread;

fn thread_waker() -> Waker {
    let thread = thread::current();
    // When wake() is called, it unparks the thread
    // (simplified — real implementation needs vtable)
    todo!()
}
```

### Real-world: tokio's Waker

tokio registers the task with an I/O driver (epoll/kqueue/IOCP). When the OS signals readiness, the driver calls `waker.wake()` which schedules the task on the thread pool.

## Pin/Unpin Deep Dive

### Why Self-Referential Structs Break

```rust
struct SelfRef {
    data: String,
    // This pointer points to `data` field above
    ptr_to_data: *const String,
}

let mut s = SelfRef { data: "hi".into(), ptr_to_data: std::ptr::null() };
s.ptr_to_data = &s.data as *const String;

let moved = s;  // s is moved! ptr_to_data still points to old location!
// UNDEFINED BEHAVIOR: ptr_to_data is dangling
```

### How async generates self-referential types

```rust
async fn example() {
    let buffer = vec![0u8; 1024];
    let slice = &buffer[..];     // slice references buffer
    read_into(slice).await;      // future holds both buffer AND slice
    // The generated state machine struct has buffer AND a reference to it
}
```

### Pin Guarantees

`Pin<P>` means: the value pointed to by `P` will not be moved unless `T: Unpin`.

```rust
// Safe: T: Unpin — can still move freely
let mut x = 42i32;
let pinned = Pin::new(&mut x);
let moved = *pinned;  // OK

// Unsafe: T: !Unpin — cannot move once pinned
// Only way to create Pin<&mut T> for !Unpin T is unsafe:
let pinned = unsafe { Pin::new_unchecked(&mut my_future) };
// Or use the safe Box::pin:
let pinned: Pin<Box<MyFuture>> = Box::pin(MyFuture::new());
```

### Pin Projection Rules

For a pinned struct, which fields are also pinned?

```rust
// Using pin-project crate (safe):
use pin_project::pin_project;

#[pin_project]
struct TimedFuture<F: Future> {
    #[pin]      // structurally pinned — stays pinned
    inner: F,
    start: Instant,  // NOT pinned — can be moved/replaced
}

impl<F: Future> Future for TimedFuture<F> {
    type Output = (F::Output, Duration);

    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        let this = self.project();
        // this.inner: Pin<&mut F>  — safe to poll
        // this.start: &mut Instant — safe to read/write
        match this.inner.poll(cx) {
            Poll::Ready(val) => Poll::Ready((val, this.start.elapsed())),
            Poll::Pending => Poll::Pending,
        }
    }
}
```

## Combinators: Building Futures from Futures

```rust
use futures::future::{self, FutureExt};

// Map the output
let doubled = my_future.map(|x| x * 2);

// Chain futures
let chained = first_future.then(|result| second_future(result));

// Fuse: make safe to poll after completion
let mut fused = my_future.fuse();

// Shared: clone a future for multiple consumers
let shared = my_future.shared();
let clone1 = shared.clone();
let clone2 = shared.clone();
```

## Boxed Futures (Type Erasure)

```rust
use std::pin::Pin;
use std::future::Future;

type BoxFuture<'a, T> = Pin<Box<dyn Future<Output = T> + Send + 'a>>;

// Useful for trait methods (can't use impl Trait in trait return position pre-1.75)
trait Service {
    fn call(&self, req: Request) -> BoxFuture<'_, Response>;
}

impl Service for MyService {
    fn call(&self, req: Request) -> BoxFuture<'_, Response> {
        Box::pin(async move {
            // ... process request
            Response::new()
        })
    }
}
```
