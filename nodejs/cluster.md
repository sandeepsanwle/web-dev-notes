# Cluster

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

The **`cluster` module** lets you create multiple child processes (workers) that all share the same server port, enabling a Node.js application to take advantage of **multi-core CPUs** by running several instances of the event loop in parallel.

## 2. Simple Explanation

By default, Node.js runs on a single CPU core, even if your machine has 8. Clustering is like cloning your app into multiple copies (one per core) that all listen on the same port. The OS shares incoming connections among them, so your app can handle far more traffic at once.

## 3. Why It Is Used

- Utilize **all CPU cores** instead of just one.
- Increase **throughput** and handle more concurrent requests.
- Improve **resilience** — if one worker crashes, others keep serving.
- Achieve near-linear scaling for CPU-bound and high-traffic workloads.

## 4. Key Points

- One **primary (master)** process forks multiple **worker** processes.
- Workers share the same server port via the primary's handle.
- Workers are **separate processes** with separate memory (no shared state).
- The primary load-balances connections (round-robin on most platforms).
- Use shared stores (Redis) for sessions/state since memory isn't shared.

### Cluster Roles

| Role | Responsibility |
|------|----------------|
| Primary (master) | Forks workers, distributes connections, monitors them |
| Worker | Runs the actual server, handles requests |

## 5. Syntax

```js
const cluster = require('cluster');
const os = require('os');

if (cluster.isPrimary) {
  // fork one worker per CPU core
  for (let i = 0; i < os.cpus().length; i++) cluster.fork();
} else {
  // worker code: start the server
}
```

## 6. Example

```js
const cluster = require('cluster');
const http = require('http');
const os = require('os');

const numCPUs = os.cpus().length;

if (cluster.isPrimary) {
  console.log(`Primary ${process.pid} starting ${numCPUs} workers`);

  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  // Restart a worker if it dies (resilience)
  cluster.on('exit', (worker) => {
    console.log(`Worker ${worker.process.pid} died, restarting...`);
    cluster.fork();
  });
} else {
  // Each worker runs its own HTTP server on the SAME port
  http
    .createServer((req, res) => {
      res.end(`Handled by worker ${process.pid}\n`);
    })
    .listen(3000);

  console.log(`Worker ${process.pid} started`);
}
```

## 7. Real World Use Case

A high-traffic API running on an 8-core server. Instead of one Node process leaving 7 cores idle, clustering forks 8 workers that share port 443. Incoming requests are distributed across all workers, roughly 8x-ing throughput. If a worker crashes from an unexpected error, the primary immediately forks a replacement, so the service stays available. In production this is often managed by **PM2** (`pm2 start app.js -i max`).

## 8. Interview Questions

**Q1:** Why do we need the cluster module if Node.js is asynchronous?
**A:** Async I/O helps with concurrency on a single core, but JavaScript execution is still single-threaded, so one process can only use one CPU core. Clustering runs multiple processes to utilize all cores, increasing throughput and using available hardware fully.

**Q2:** How do cluster workers share a server port?
**A:** The primary process creates the listening socket and passes the connection handle to workers. On most platforms the primary accepts connections and distributes them round-robin; the workers don't each bind the port independently — they share the primary's handle.

**Q3:** Do cluster workers share memory? What's the implication?
**A:** No. Each worker is a separate process with its own memory and event loop. So in-memory state (sessions, caches, counters) is **not** shared between workers. You must use an external store like Redis for shared state and sticky sessions or a shared session store.

**Q4:** What is the difference between `cluster` and `worker_threads`?
**A:** `cluster` forks full processes (separate memory, good for scaling I/O-bound servers across cores). `worker_threads` creates threads within one process that can **share memory** via `SharedArrayBuffer`, which is better for CPU-bound parallel computation with lower overhead.

**Q5:** How do you keep a clustered app resilient when workers crash?
**A:** Listen for the `exit` event on the primary and `cluster.fork()` a replacement worker. In production, process managers like PM2 automate this, plus provide zero-downtime reloads, monitoring, and log management.

## 9. Common Mistakes

- Storing sessions/state in worker memory, breaking across workers.
- Forking more workers than CPU cores, causing context-switch overhead.
- Not restarting dead workers, slowly losing capacity.
- Assuming `cluster` helps CPU-bound tasks inside a single request (use `worker_threads`).
- Forgetting sticky sessions for WebSocket/stateful connections behind a cluster.

## 10. Advanced Notes

- Use **PM2** or container orchestration (Kubernetes) instead of hand-rolling cluster logic in production.
- For WebSockets, enable **sticky sessions** so a client stays on the same worker, or use a shared adapter (e.g. Redis pub/sub).
- `cluster.schedulingPolicy` can be `SCHED_RR` (round-robin) or `SCHED_NONE` (OS decides).
- Combine clustering (across cores) with horizontal scaling (across machines) behind a load balancer.
- Implement **graceful shutdown**: stop accepting new connections, finish in-flight requests, then exit.
- `worker_threads` is preferable for CPU-heavy work; `cluster` is for scaling the network/server layer.
