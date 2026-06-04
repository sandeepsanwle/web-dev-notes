# Streams

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

A **stream** is an abstract interface in Node.js for working with streaming data — reading or writing data piece by piece (in chunks) instead of loading everything into memory at once.

## 2. Simple Explanation

Think of watching a video online. You don't download the entire movie before it starts; it plays as the data arrives in small pieces. Streams work the same way: data flows through your program chunk by chunk, so you can start processing immediately and use very little memory.

## 3. Why It Is Used

- **Memory efficiency**: process gigabyte files without loading them fully into RAM.
- **Time efficiency**: start working on data as soon as the first chunk arrives.
- **Composability**: pipe streams together to build data pipelines.
- Foundation for HTTP requests/responses, file I/O, and compression in Node.js.

## 4. Key Points

- Four stream types: **Readable**, **Writable**, **Duplex**, **Transform**.
- Streams emit events: `data`, `end`, `error`, `finish`, `close`.
- `pipe()` connects a readable stream to a writable stream and handles backpressure.
- **Backpressure** prevents a fast producer from overwhelming a slow consumer.
- `stream.pipeline()` is the modern, safe way to chain streams with error handling.

### Stream Types

| Type | Description | Example |
|------|-------------|---------|
| Readable | Source you read from | `fs.createReadStream`, HTTP request |
| Writable | Destination you write to | `fs.createWriteStream`, HTTP response |
| Duplex | Both readable and writable | TCP socket (`net.Socket`) |
| Transform | Duplex that modifies data | `zlib.createGzip`, crypto cipher |

## 5. Syntax

```js
const fs = require('fs');

// Readable
const readable = fs.createReadStream('input.txt', { encoding: 'utf8' });

// Writable
const writable = fs.createWriteStream('output.txt');

// Pipe: readable -> writable
readable.pipe(writable);
```

## 6. Example

```js
const fs = require('fs');
const zlib = require('zlib');
const { pipeline } = require('stream');

// Compress a file using a stream pipeline (memory efficient)
pipeline(
  fs.createReadStream('big-file.txt'),
  zlib.createGzip(),               // Transform stream
  fs.createWriteStream('big-file.txt.gz'),
  (err) => {
    if (err) {
      console.error('Pipeline failed:', err);
    } else {
      console.log('Pipeline succeeded — file compressed');
    }
  }
);
```

## 7. Real World Use Case

A file upload/download service. When a user downloads a 2 GB video, the server uses `fs.createReadStream(file).pipe(res)` to stream bytes directly to the HTTP response. The server never holds the whole file in memory, so it can serve many large downloads simultaneously without crashing. The same pattern powers log processing, CSV imports, and on-the-fly image/video transcoding.

## 8. Interview Questions

**Q1:** What are the four types of streams in Node.js?
**A:** Readable (read data from a source), Writable (write data to a destination), Duplex (both readable and writable, like a TCP socket), and Transform (a Duplex stream that transforms data as it passes through, like gzip).

**Q2:** What is backpressure and how do streams handle it?
**A:** Backpressure happens when a writable stream can't consume data as fast as a readable stream produces it. `pipe()` and `pipeline()` handle it automatically by pausing the readable stream when the writable's internal buffer is full and resuming when it drains.

**Q3:** What is the difference between `pipe()` and `pipeline()`?
**A:** Both connect streams, but `pipeline()` (added in Node 10) properly propagates errors and cleans up all streams if any one fails. With `pipe()`, errors are not forwarded automatically, which can cause memory leaks from un-destroyed streams.

**Q4:** What is the difference between flowing and paused mode for a Readable stream?
**A:** In **flowing** mode data is read automatically and emitted via `data` events. In **paused** mode you must explicitly call `read()` to pull chunks. Attaching a `data` listener or calling `pipe()` switches a stream to flowing mode.

**Q5:** Why use streams instead of reading a whole file with `fs.readFile`?
**A:** `fs.readFile` loads the entire file into memory, which fails or is slow for large files. Streams process data in chunks, keeping memory usage low and constant regardless of file size, and let processing begin before the full file is available.

## 9. Common Mistakes

- Not handling the `error` event, causing crashes or leaks.
- Using `pipe()` without error handling instead of `pipeline()`.
- Ignoring backpressure by writing in a tight loop without checking the return value of `write()`.
- Mixing flowing and paused mode logic, losing data chunks.
- Reading huge files with `fs.readFile` and running out of memory.

## 10. Advanced Notes

- Async iterators: `for await (const chunk of readable)` is a clean, modern way to consume readable streams.
- `highWaterMark` controls the internal buffer size (default 16 KB for byte streams, 16 objects for object mode).
- **Object mode** (`objectMode: true`) lets streams pass JS objects instead of buffers/strings.
- `stream.pipeline` also has a Promise version: `require('stream/promises').pipeline`.
- Implement custom streams by extending `Readable`/`Writable`/`Transform` and defining `_read`, `_write`, or `_transform`.
