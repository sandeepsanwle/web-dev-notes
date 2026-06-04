# File Upload

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition

File upload is the process of receiving files (images, PDFs, videos) from a client through an HTTP request — typically a `multipart/form-data` POST — and storing them on disk, in memory, or in cloud storage. Express handles this with middleware like **Multer**.

## 2. Simple Explanation

Uploading a file is like mailing a package. The client wraps the file in a special envelope (`multipart/form-data`), the postal service (HTTP) delivers it, and a worker (Multer) opens the envelope, checks it's allowed, and files it in the right cabinet (disk or cloud).

## 3. Why It Is Used

- To let users upload profile pictures, documents, attachments, etc.
- To process media (resize images, scan PDFs).
- To integrate with cloud storage (S3, Cloudinary).
- To support import features (CSV/Excel uploads).

## 4. Key Points

- File uploads use `multipart/form-data`, not JSON, so `express.json()` won't parse them.
- **Multer** is the standard middleware for handling multipart data.
- Choose storage: `diskStorage` (saves to disk) or `memoryStorage` (buffer in RAM).
- Always set **limits** (file size) and a **fileFilter** (allowed types).
- The uploaded file info is on `req.file` (single) or `req.files` (multiple).

## 5. Syntax

```js
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });

app.post('/upload', upload.single('file'), handler);   // one file
app.post('/upload', upload.array('files', 5), handler); // multiple
```

## 6. Example

```js
const express = require('express');
const multer = require('multer');
const path = require('path');
const app = express();

const storage = multer.diskStorage({
  destination: (req, file, cb) => cb(null, 'uploads/'),
  filename: (req, file, cb) => {
    const unique = Date.now() + path.extname(file.originalname);
    cb(null, unique);
  }
});

const upload = multer({
  storage,
  limits: { fileSize: 5 * 1024 * 1024 }, // 5 MB
  fileFilter: (req, file, cb) => {
    const allowed = ['image/jpeg', 'image/png'];
    cb(null, allowed.includes(file.mimetype)); // reject others
  }
});

app.post('/avatar', upload.single('avatar'), (req, res) => {
  if (!req.file) return res.status(400).json({ error: 'No file' });
  res.json({ filename: req.file.filename, size: req.file.size });
});

// Multer error handler
app.use((err, req, res, next) => {
  if (err instanceof multer.MulterError) {
    return res.status(400).json({ error: err.message });
  }
  next(err);
});

app.listen(3000);
```

## 7. Real World Use Case

A social media app lets users upload profile photos. Multer uses `memoryStorage` to keep the file as a buffer, the app validates it's an image under 5 MB, resizes it with `sharp`, then streams it to AWS S3 and stores only the resulting URL in the database. The buffer is discarded, so no temporary files clutter the server disk.

## 8. Interview Questions

**Q1:** Why can't `express.json()` parse file uploads?
**A:** File uploads use the `multipart/form-data` content type, which encodes binary file data and fields differently from JSON. You need multipart-aware middleware like Multer to parse it.

**Q2:** What's the difference between Multer's disk and memory storage?
**A:** `diskStorage` writes files straight to the filesystem (good for large files, low memory). `memoryStorage` keeps the file as a Buffer in RAM (convenient for processing/forwarding to cloud, but risky for large files).

**Q3:** How do you restrict file types and sizes?
**A:** Pass `limits: { fileSize }` and a `fileFilter` callback to Multer. The filter inspects `file.mimetype`/`originalname` and calls `cb(null, false)` to reject disallowed files.

**Q4:** How do you handle multiple files?
**A:** Use `upload.array('field', maxCount)` for several files under one field, or `upload.fields([...])` for multiple named fields. Files appear on `req.files`.

**Q5:** Why is validating MIME type alone not fully secure?
**A:** The client can spoof the `Content-Type` header. For stronger checks, verify the file's magic bytes/signature server-side and never execute or serve uploads from a path where they could run as code.

## 9. Common Mistakes

- Forgetting to set file size limits, enabling DoS via huge uploads.
- Trusting client-provided filenames (path traversal) — sanitize/rename them.
- Storing uploads in a publicly executable directory.
- Relying solely on the MIME type sent by the client.
- Not handling `MulterError` separately, returning unclear errors.

## 10. Advanced Notes

- **Streaming to cloud:** Pipe uploads directly to S3/GCS to avoid local disk usage.
- **Virus scanning:** Integrate ClamAV for user-generated uploads.
- **Image processing:** Use `sharp` for resizing/format conversion before storage.
- **Signed URLs:** For large files, let clients upload directly to cloud via pre-signed URLs, bypassing your server.
- **Chunked/resumable uploads:** Use tus or multipart S3 uploads for very large files.
- Always store metadata (URL, size, owner) in the DB, not the binary itself.
