# File System

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

The **File System (`fs`) module** is a built-in Node.js module that lets you interact with the file system — creating, reading, updating, deleting, and watching files and directories.

## 2. Simple Explanation

The `fs` module is your program's hands for touching files on disk. Want to read a config file, save a log, or list a folder's contents? `fs` gives you functions to do exactly that, in both blocking (sync) and non-blocking (async) flavors.

## 3. Why It Is Used

- Read configuration, templates, and data files at runtime.
- Persist data: logs, uploads, generated reports, caches.
- Manage directories and file metadata (size, permissions, timestamps).
- Watch files for changes to trigger rebuilds or reloads.

## 4. Key Points

- Three API styles: **callback** (`fs.readFile`), **promise** (`fs.promises`), and **sync** (`fs.readFileSync`).
- Async methods are non-blocking; sync methods block the event loop.
- Use streams (`createReadStream`/`createWriteStream`) for large files.
- Always handle errors — files may not exist or be inaccessible.
- Paths should be built with the `path` module for cross-platform safety.

### API Styles

| Style | Example | Blocks event loop? |
|-------|---------|--------------------|
| Sync | `fs.readFileSync('a.txt')` | Yes |
| Callback | `fs.readFile('a.txt', cb)` | No |
| Promise | `await fs.promises.readFile('a.txt')` | No |

## 5. Syntax

```js
const fs = require('fs');
const fsp = require('fs/promises');

// Async callback
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) throw err;
  console.log(data);
});

// Promise / async-await
const data = await fsp.readFile('file.txt', 'utf8');
```

## 6. Example

```js
const fsp = require('fs/promises');
const path = require('path');

async function main() {
  const filePath = path.join(__dirname, 'notes.txt');

  // Write a file
  await fsp.writeFile(filePath, 'Hello FS\n');

  // Append to it
  await fsp.appendFile(filePath, 'Second line\n');

  // Read it back
  const content = await fsp.readFile(filePath, 'utf8');
  console.log(content);

  // Get metadata
  const stats = await fsp.stat(filePath);
  console.log('Size:', stats.size, 'bytes');

  // List a directory
  const files = await fsp.readdir(__dirname);
  console.log('Dir contains:', files);
}

main().catch(console.error);
```

## 7. Real World Use Case

A logging system in a web app. Each incoming request appends a line to a daily log file using `fs.appendFile` (or a write stream for high volume). On startup, the app reads a `config.json` with `fs.promises.readFile`. A backup job reads large data files via `createReadStream` and pipes them to compressed archives — never loading full files into memory.

## 8. Interview Questions

**Q1:** What is the difference between `fs.readFile` and `fs.readFileSync`?
**A:** `fs.readFile` is asynchronous and non-blocking — it takes a callback (or returns a promise via `fs.promises`) and lets the event loop continue. `fs.readFileSync` is synchronous and blocks the entire event loop until the file is fully read, which can hurt server throughput.

**Q2:** When should you use streams instead of `fs.readFile`?
**A:** Use streams for large files or continuous data. `fs.readFile` loads the whole file into memory at once, while `createReadStream` processes it in chunks, keeping memory usage low and letting you start processing before reading completes.

**Q3:** How do you safely build file paths across operating systems?
**A:** Use the `path` module — `path.join(__dirname, 'sub', 'file.txt')` — which uses the correct separator (`/` or `\`) for the OS and normalizes the path, instead of manually concatenating strings.

**Q4:** What does `fs.stat()` return and when is it useful?
**A:** It returns a `Stats` object with metadata like `size`, `mtime`, `birthtime`, and helpers like `isFile()` and `isDirectory()`. It's useful for checking whether a path exists, its type, and how big or old a file is.

**Q5:** How can you check if a file exists in Node.js?
**A:** Prefer attempting the operation and handling the error, or use `fs.promises.access(path)` which rejects if the file isn't accessible. Avoid `fs.exists` (deprecated) and avoid check-then-act race conditions by handling `ENOENT` errors directly.

## 9. Common Mistakes

- Using sync methods (`readFileSync`) in request handlers, blocking the server.
- Concatenating paths with `+` and `/` instead of using `path.join`.
- Not handling `ENOENT` and other errors, causing crashes.
- Reading huge files with `readFile`, exhausting memory.
- Race conditions from check-then-act (e.g. `fs.exists` then `fs.readFile`).

## 10. Advanced Notes

- `fs.watch` (and `fs.watchFile`) monitor changes but behave differently across platforms; libraries like `chokidar` smooth over the inconsistencies.
- Use file descriptors (`fs.open`/`read`/`write`/`close`) for fine-grained control and partial reads/writes.
- `fs.promises.cp` (Node 16+) copies files/directories recursively.
- Set proper flags (`'a'`, `'w'`, `'r+'`) and mode (permissions) when opening files.
- For atomic writes, write to a temp file then `fs.rename` it into place to avoid partial/corrupt files.
