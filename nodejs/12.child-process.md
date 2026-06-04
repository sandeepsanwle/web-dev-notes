# Child Process

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

The **`child_process` module** lets a Node.js program spawn and communicate with separate subprocesses — running external commands, scripts, or other Node programs in their own OS process.

## 2. Simple Explanation

Sometimes your Node app needs help from another program — running a shell command, calling Python, or doing heavy work without freezing the main app. Child processes are like hiring assistants: you hand off a task to a separate worker process, and it reports back when done, while your main app stays responsive.

## 3. Why It Is Used

- Run **external commands/programs** (git, ffmpeg, python).
- Offload **CPU-intensive work** so the main event loop isn't blocked.
- Achieve **parallelism** across multiple processes/cores.
- Build pipelines and orchestrate other tools from Node.

## 4. Key Points

- Four creation methods: **`spawn`**, **`exec`**, **`execFile`**, **`fork`**.
- `spawn` streams output (good for large/long output); `exec` buffers it.
- `fork` is a special case for spawning **Node scripts** with an IPC channel.
- Child processes have **separate memory**; communicate via streams or IPC messages.
- Avoid `exec` with unsanitized input — **shell injection** risk.

### Methods Compared

| Method | Use case | Output | Shell? | IPC? |
|--------|----------|--------|--------|------|
| `spawn` | Long-running, large output | Streamed | No (default) | No |
| `exec` | Short shell command | Buffered | Yes | No |
| `execFile` | Run a binary directly | Buffered | No | No |
| `fork` | Spawn a Node module | Streamed | No | Yes (`message`) |

## 5. Syntax

```js
const { spawn, exec, execFile, fork } = require('child_process');

// spawn: stream output
const child = spawn('ls', ['-lh', '/usr']);

// exec: buffered output with a callback
exec('node -v', (err, stdout, stderr) => console.log(stdout));

// fork: spawn another Node script with IPC
const worker = fork('worker.js');
```

## 6. Example

```js
const { spawn } = require('child_process');

// Run an external command and stream its output
const child = spawn('node', ['-e', "console.log('Hello from child')"]);

child.stdout.on('data', (data) => {
  console.log(`stdout: ${data}`);
});

child.stderr.on('data', (data) => {
  console.error(`stderr: ${data}`);
});

child.on('close', (code) => {
  console.log(`Child exited with code ${code}`);
});

// --- fork example with IPC ---
const { fork } = require('child_process');
const worker = fork(__filename.replace('parent', 'worker')); // pseudo path

worker.on('message', (msg) => console.log('From worker:', msg));
worker.send({ task: 'compute', value: 42 }); // send a message to the child
```

## 7. Real World Use Case

A video-processing service receives an uploaded video and needs to transcode it. The Node server uses `spawn('ffmpeg', [...args])` to run FFmpeg as a child process, streaming its progress output back to the client. Because FFmpeg runs in its own process, the heavy CPU work doesn't block the Node event loop, so the server keeps handling other API requests during transcoding.

## 8. Interview Questions

**Q1:** What is the difference between `spawn` and `exec`?
**A:** `spawn` returns a stream and is ideal for long-running processes or large output because data flows incrementally. `exec` buffers all output in memory and invokes a callback when the process finishes, which is convenient for short commands but risky for large output (buffer overflow).

**Q2:** When would you use `fork` instead of `spawn`?
**A:** Use `fork` to spawn another **Node.js script**. It's a special case of `spawn` that automatically sets up an **IPC channel**, so the parent and child can exchange messages with `child.send()` and `process.on('message')`. It's commonly used for worker processes.

**Q3:** Why is `exec` with user input dangerous?
**A:** `exec` runs its command through a shell, so unsanitized user input can inject additional commands (shell injection), e.g. `; rm -rf /`. Prefer `execFile`/`spawn` with an argument array (no shell) and validate/escape inputs.

**Q4:** How do parent and child processes communicate?
**A:** Via the child's standard streams (`stdout`, `stderr`, `stdin`) for spawn/exec, or via an **IPC channel** with `fork` (and `spawn` with `stdio: 'ipc'`), using `send()` to pass serializable messages and listening on the `message` event.

**Q5:** What's the difference between `child_process`, `cluster`, and `worker_threads`?
**A:** `child_process` runs arbitrary external programs or Node scripts as separate processes. `cluster` forks Node server processes that share a port for load balancing. `worker_threads` runs threads within one process that can share memory — best for CPU-bound JS work with lower overhead than separate processes.

## 9. Common Mistakes

- Using `exec` with untrusted input (shell injection vulnerability).
- Buffering huge output with `exec` and hitting `maxBuffer` limits.
- Not handling `error`, `stderr`, and non-zero `exit` codes.
- Forgetting child processes have separate memory (no shared variables).
- Leaving zombie/orphan processes by not closing or killing children.

## 10. Advanced Notes

- Set `maxBuffer` for `exec`/`execFile` to handle larger output, or switch to `spawn`.
- Use `stdio` options (`'inherit'`, `'pipe'`, `'ignore'`, `'ipc'`) to control stream wiring.
- `spawn(cmd, args, { shell: true })` enables shell features but reintroduces injection risk.
- Promisify with `util.promisify(exec)` for async/await ergonomics.
- For CPU-bound JS, prefer `worker_threads` (shared memory, lower overhead) over forking processes.
- Always implement graceful termination: handle signals and call `child.kill()` on shutdown to avoid orphans.
