# Event Loop

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

The **event loop** is the mechanism that allows Node.js to perform non-blocking, asynchronous I/O operations even though JavaScript runs on a single thread. It continuously checks for and processes events (callbacks) from various queues in a well-defined order.

## 2. Simple Explanation

Imagine a waiter (single thread) at a restaurant. Instead of standing idle while the kitchen cooks an order, the waiter takes other orders, serves drinks, and comes back when a dish is ready. The event loop is that waiter: it hands off slow work (file reads, network calls) to the system and keeps serving other requests, picking up the results later via callbacks.

## 3. Why It Is Used

- Enables **high concurrency** with a single thread (no thread-per-request overhead).
- Keeps the application **responsive** by never blocking on I/O.
- Powers Node.js's reputation for scalable network applications.
- Lets thousands of connections be handled efficiently with low memory.

## 4. Key Points

- Node.js uses **libuv** (a C library) to implement the event loop and a thread pool.
- The event loop has **6 main phases**, each with its own callback queue.
- `process.nextTick()` and Promise microtasks run **between** phases, before the loop continues.
- CPU-heavy synchronous code **blocks** the event loop — offload it.
- The thread pool (default size 4) handles `fs`, `crypto`, `dns`, and `zlib` work.

## 5. Syntax

```js
// Macrotasks vs microtasks ordering
setTimeout(() => console.log('timeout'), 0); // timers phase
setImmediate(() => console.log('immediate')); // check phase
process.nextTick(() => console.log('nextTick')); // microtask (highest priority)
Promise.resolve().then(() => console.log('promise')); // microtask
```

## 6. Example

```js
console.log('1: start');

setTimeout(() => console.log('4: setTimeout'), 0);

Promise.resolve().then(() => console.log('3: promise'));

process.nextTick(() => console.log('2: nextTick'));

console.log('1.5: end of sync');

// Output order:
// 1: start
// 1.5: end of sync
// 2: nextTick      (microtask: nextTick queue first)
// 3: promise       (microtask: promise queue)
// 4: setTimeout    (timers phase, next loop tick)
```

### Event Loop Phases (ASCII diagram)

```
   ┌───────────────────────────┐
┌─>│           timers          │  setTimeout(), setInterval()
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │  deferred I/O callbacks
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │  internal use only
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll            │<─────│  connections, │
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check           │  setImmediate()
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │  socket.on('close', ...)
   └───────────────────────────┘

  (After EACH phase: process.nextTick queue, then Promise microtasks)
```

## 7. Real World Use Case

A web server handling 10,000 concurrent connections. When a request needs a database query, Node.js dispatches the query and immediately moves on to other requests instead of blocking. When the DB responds, the callback is queued and executed in the **poll** phase. This is why a single Node.js process can serve massive traffic with minimal resources, ideal for API gateways and real-time apps.

## 8. Interview Questions

**Q1:** What is the event loop and why is it important in Node.js?
**A:** It is a loop provided by libuv that orchestrates the execution of asynchronous callbacks across several phases. It is important because it lets single-threaded JavaScript handle many I/O operations concurrently without blocking, enabling scalable servers.

**Q2:** What is the difference between `setTimeout(fn, 0)` and `setImmediate(fn)`?
**A:** `setTimeout(fn, 0)` schedules the callback in the **timers** phase, while `setImmediate(fn)` schedules it in the **check** phase. Inside an I/O callback, `setImmediate` always runs before `setTimeout`. At the top level, their order is non-deterministic.

**Q3:** How does `process.nextTick()` differ from `Promise.resolve().then()`?
**A:** Both are microtasks that run between event loop phases, but the `nextTick` queue is processed **before** the Promise microtask queue. Overusing `process.nextTick` can starve the event loop because it keeps running before the loop continues.

**Q4:** What happens if you run CPU-intensive synchronous code in Node.js?
**A:** It **blocks** the event loop, so no other callbacks (including incoming requests) can be processed until it finishes. The fix is to offload work to worker threads, child processes, or break it into smaller async chunks.

**Q5:** What is libuv's thread pool used for?
**A:** It handles operations that have no async OS primitive, such as `fs` file operations, `crypto` (pbkdf2), `dns.lookup`, and `zlib`. Its default size is 4 and can be tuned via the `UV_THREADPOOL_SIZE` environment variable.

## 9. Common Mistakes

- Assuming Node.js is fully multi-threaded — JS execution is single-threaded.
- Blocking the loop with heavy `JSON.parse`, loops, or sync crypto.
- Overusing `process.nextTick`, starving I/O callbacks.
- Expecting `setTimeout(fn, 0)` to run "immediately" — it waits for the timers phase.
- Forgetting that microtasks drain completely between each phase.

## 10. Advanced Notes

- Tune `UV_THREADPOOL_SIZE` (max 1024) when doing many concurrent `fs`/`crypto` operations.
- Use `worker_threads` for CPU-bound parallelism that shares memory via `SharedArrayBuffer`.
- Microtask starvation: an infinite chain of `process.nextTick`/Promises can prevent the loop from ever reaching the poll phase.
- The **poll** phase will block and wait for I/O if there are no timers or immediates pending, which is how Node.js idles efficiently.
- Tools like `--trace-event-categories` and `perf_hooks` help measure event loop lag (`perf_hooks.monitorEventLoopDelay`).
