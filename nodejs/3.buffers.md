# Buffers

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

A **Buffer** is a fixed-size chunk of memory allocated outside the V8 JavaScript heap, used to store and manipulate **raw binary data** (sequences of bytes) in Node.js.

## 2. Simple Explanation

JavaScript was built for text and numbers, not raw bytes. But servers constantly deal with binary data — files, images, network packets. A Buffer is like a small array of bytes (0–255 each) that lets you read and write that raw data directly, byte by byte.

## 3. Why It Is Used

- Handle **binary data** that strings can't represent safely.
- Work with TCP streams, file system reads, image/video bytes, and protocols.
- Convert between **encodings** (utf8, base64, hex, latin1).
- Operate efficiently on raw memory without V8 garbage-collection overhead per byte.

## 4. Key Points

- A Buffer is an array of bytes; each element is an integer from `0` to `255`.
- Buffers have a **fixed size** once created (you can't resize them).
- `Buffer.from()` and `Buffer.alloc()` are the safe ways to create buffers.
- `Buffer.alloc(n)` is zero-filled; `Buffer.allocUnsafe(n)` is faster but may contain old memory.
- Buffers are instances of `Uint8Array`, so TypedArray methods work on them.

## 5. Syntax

```js
// Create a zero-filled buffer of 10 bytes
const buf1 = Buffer.alloc(10);

// Create from a string (default utf8)
const buf2 = Buffer.from('Hello');

// Create from an array of bytes
const buf3 = Buffer.from([72, 105]); // "Hi"

// Convert buffer back to string
console.log(buf2.toString('utf8'));   // "Hello"
console.log(buf2.toString('base64')); // "SGVsbG8="
```

## 6. Example

```js
// Encoding conversion and byte manipulation
const buf = Buffer.from('Node.js', 'utf8');

console.log(buf);               // <Buffer 4e 6f 64 65 2e 6a 73>
console.log(buf.length);        // 7 (bytes)
console.log(buf.toString('hex'));    // 4e6f64652e6a73
console.log(buf.toString('base64')); // Tm9kZS5qcw==

// Modify a byte
buf[0] = 0x6e; // lowercase 'n'
console.log(buf.toString()); // "node.js"

// Concatenate buffers
const combined = Buffer.concat([Buffer.from('Hello '), Buffer.from('World')]);
console.log(combined.toString()); // "Hello World"
```

## 7. Real World Use Case

A service that accepts image uploads as base64 strings (common in JSON APIs). The server converts the base64 string to a Buffer with `Buffer.from(data, 'base64')`, then writes those raw bytes to disk or to cloud storage. Buffers are also essential when computing file hashes, parsing binary protocols (like MQTT), or building/decoding network packets.

## 8. Interview Questions

**Q1:** What is a Buffer in Node.js and why does it exist?
**A:** A Buffer is a fixed-length container for raw binary data stored outside the V8 heap. It exists because JavaScript strings cannot reliably represent arbitrary binary data, and servers need to handle bytes from files, sockets, and protocols.

**Q2:** What is the difference between `Buffer.alloc()` and `Buffer.allocUnsafe()`?
**A:** `Buffer.alloc(size)` returns a zero-filled buffer, which is safe but slightly slower. `Buffer.allocUnsafe(size)` skips zero-filling so it's faster but may expose old, sensitive memory contents — you must overwrite it fully before use.

**Q3:** How do you convert a Buffer to a string and vice versa?
**A:** Use `buf.toString(encoding)` to decode bytes into a string and `Buffer.from(str, encoding)` to encode a string into bytes. Supported encodings include `utf8`, `base64`, `hex`, `latin1`, and `ascii`.

**Q4:** Are Buffers resizable? How are they related to TypedArrays?
**A:** No, Buffers have a fixed size once allocated. A Buffer is a subclass of `Uint8Array`, so it shares its API and underlying `ArrayBuffer`, but adds Node-specific methods like `toString(encoding)` and `write()`.

**Q5:** Why should you be careful with `Buffer.allocUnsafe()` for security?
**A:** Because it returns memory that hasn't been cleared, it may contain leftover data from previous allocations, including passwords or tokens. If you send that buffer without fully overwriting it, you can leak sensitive information.

## 9. Common Mistakes

- Using the deprecated `new Buffer()` constructor (security risk) instead of `Buffer.from`/`alloc`.
- Forgetting to specify or mismatching encodings, producing garbled text.
- Assuming `buf.length` equals character count — it counts **bytes**, and UTF-8 chars can be multiple bytes.
- Using `allocUnsafe` and sending uninitialized memory.
- Trying to resize a buffer instead of creating a new one or using `Buffer.concat`.

## 10. Advanced Notes

- Buffers share memory with their underlying `ArrayBuffer`; `buf.slice()`/`subarray()` returns a view, not a copy — mutations affect the original.
- `Buffer.poolSize` (default 8 KB) controls a shared internal memory pool used by small `allocUnsafe` allocations.
- For multi-byte numeric reads/writes use methods like `readUInt32BE`, `writeInt16LE` to control endianness.
- In modern code, prefer `Blob`, `TextEncoder`/`TextDecoder`, or `Uint8Array` for cross-platform (browser + Node) compatibility.
- Large/long-lived buffers live outside the V8 heap, so they aren't subject to the same GC pressure but can still cause memory growth if leaked.
