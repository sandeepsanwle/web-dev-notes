# Modules

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

A **module** in Node.js is a reusable, self-contained unit of code (usually a single file) that encapsulates related functionality and exposes it to other files through an export/import system.

## 2. Simple Explanation

Modules are like LEGO bricks for your code. Instead of writing one giant file, you split logic into small files that each do one job and "export" what they offer. Other files "import" only what they need. This keeps code organized, reusable, and easy to maintain.

## 3. Why It Is Used

- **Encapsulation**: each module has its own scope; no global variable clashes.
- **Reusability**: write once, import anywhere.
- **Maintainability**: small focused files are easier to read and test.
- **Dependency management**: clearly declares what code relies on what.

## 4. Key Points

- Two systems: **CommonJS** (`require`/`module.exports`) and **ES Modules** (`import`/`export`).
- CommonJS is the historical Node default; ESM is the modern standard.
- File type is decided by extension (`.cjs`, `.mjs`) or `"type"` in `package.json`.
- `require` is **synchronous**; ESM `import` is **asynchronous** and statically analyzable.
- Modules are **cached** after first load — code runs only once per process.

### CommonJS vs ES Modules

| Feature | CommonJS | ES Modules |
|---------|----------|------------|
| Import | `require()` | `import` |
| Export | `module.exports` | `export` / `export default` |
| Loading | Synchronous | Asynchronous |
| File ext | `.cjs` / `.js` | `.mjs` / `.js` (with `"type":"module"`) |
| `__dirname` | Available | Use `import.meta.url` |

## 5. Syntax

```js
// --- CommonJS ---
// math.js
function add(a, b) { return a + b; }
module.exports = { add };

// app.js
const { add } = require('./math');

// --- ES Modules ---
// math.mjs
export function add(a, b) { return a + b; }

// app.mjs
import { add } from './math.mjs';
```

## 6. Example

```js
// logger.js (CommonJS module)
let count = 0;

function log(message) {
  count++;
  console.log(`[${count}] ${message}`);
}

module.exports = { log };

// app.js
const { log } = require('./logger');

log('Server started');   // [1] Server started
log('Request received'); // [2] Request received
// 'count' is private to the module — its state persists because
// the module is cached and runs only once.
```

## 7. Real World Use Case

A typical Express app is split into modules: `routes/users.js`, `controllers/userController.js`, `models/User.js`, `config/db.js`, and `utils/logger.js`. The main `app.js` imports these modules to wire everything together. This separation lets a team work on different files in parallel and makes unit testing each piece straightforward.

## 8. Interview Questions

**Q1:** What is the difference between CommonJS and ES Modules?
**A:** CommonJS uses `require()`/`module.exports`, loads synchronously, and is Node's original system. ES Modules use `import`/`export`, load asynchronously, support static analysis (tree-shaking), and are the JavaScript standard. ESM also runs in strict mode by default and lacks `__dirname`.

**Q2:** What is `module.exports` vs `exports`?
**A:** `module.exports` is the actual object returned by `require`. `exports` is just a reference to `module.exports`. You can add properties via `exports.foo = ...`, but reassigning `exports = {...}` breaks the link, so to export a single value you must use `module.exports = ...`.

**Q3:** Are Node.js modules cached? Why does it matter?
**A:** Yes. After a module is first required, its result is stored in `require.cache` and reused on subsequent `require` calls. This means module-level code runs only once and any state (like counters or singletons) persists across imports.

**Q4:** How do you use ES Modules in a Node.js project?
**A:** Either name files with the `.mjs` extension, or set `"type": "module"` in `package.json` so `.js` files are treated as ESM. CommonJS files can then use the `.cjs` extension.

**Q5:** What is the difference between a core module, a local module, and a third-party module?
**A:** Core modules are built into Node (`fs`, `http`) and required by name. Local modules are your own files, required by relative path (`./utils`). Third-party modules are installed via npm into `node_modules` and required by name (`express`).

## 9. Common Mistakes

- Reassigning `exports = {...}` instead of `module.exports = {...}`.
- Mixing `require` and `import` in the same file without proper config.
- Circular dependencies returning incomplete (partially-loaded) exports.
- Forgetting file extensions in ESM relative imports (they're required).
- Expecting module code to re-run on each `require` (it's cached).

## 10. Advanced Notes

- **Circular dependencies**: CommonJS returns a partial export object; restructure code or use lazy `require` inside functions to avoid `undefined`.
- Use `createRequire(import.meta.url)` to call CommonJS-style `require` from within an ES module.
- Node's resolution algorithm checks core modules, then `node_modules` up the tree, then file paths.
- `package.json` `exports` field defines official entry points and can restrict deep imports.
- Dynamic `import()` returns a promise and works in both CJS and ESM for on-demand/lazy loading.
