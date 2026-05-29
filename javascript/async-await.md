# Async/Await

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition
`async/await` is syntactic sugar over Promises. An `async` function always returns a promise, and the `await` keyword pauses execution within that function until a promise settles, letting you write asynchronous code that reads synchronously.

## 2. Simple Explanation
Instead of chaining `.then()` callbacks, you write code top-to-bottom and say "wait here until this is done" with `await`. It looks like normal sequential code but doesn't block the rest of the app.

## 3. Why It Is Used
It makes asynchronous code far more readable, easier to debug (real stack traces, normal `try/catch`), and avoids deep promise chains, while still being non-blocking.

## 4. Key Points
- `await` only works inside `async` functions (or top-level in modules).
- An `async` function always returns a promise.
- Errors are caught with `try/catch`, just like synchronous code.
- `await` pauses only the current async function, not the whole program.
- Use `Promise.all` with `await` to run independent tasks in parallel.

## 5. Syntax
```js
async function name() {
  try {
    const result = await somePromise();
    return result;
  } catch (err) {
    // handle error
  }
}
```

## 6. Example
```js
function delay(ms, val) {
  return new Promise((res) => setTimeout(() => res(val), ms));
}

async function run() {
  const a = await delay(300, "A");
  const b = await delay(300, "B");
  console.log(a, b);                      // "A B" after ~600ms (sequential)

  const [x, y] = await Promise.all([      // parallel
    delay(300, "X"),
    delay(300, "Y"),
  ]);
  console.log(x, y);                      // "X Y" after ~300ms
}
run();
```
Sequential `await`s add up; `Promise.all` runs them concurrently for speed.

## 7. Real World Use Case
Fetching data from APIs in sequence or parallel, reading files, database operations in services, orchestrating multi-step workflows (authenticate → fetch profile → fetch settings).

## 8. Interview Questions
**Q1:** What does an `async` function return?
**A:** Always a promise — resolved with the return value or rejected with a thrown error.

**Q2:** Does `await` block the entire program?
**A:** No. It only pauses the current async function; the event loop continues handling other tasks.

**Q3:** How do you handle errors with async/await?
**A:** Wrap awaited calls in `try/catch`, or attach `.catch()` to the returned promise.

**Q4:** How do you run multiple async tasks in parallel?
**A:** Start them without awaiting individually and combine with `await Promise.all([...])`.

**Q5:** Can you use `await` at the top level?
**A:** Yes, in ES modules (top-level await); otherwise it must be inside an `async` function.

## 9. Common Mistakes
- Awaiting in a loop when tasks could run in parallel (slow).
- Forgetting `try/catch`, causing unhandled rejections.
- Using `forEach` with async callbacks (it doesn't await) — use `for...of`.
- Not realizing an `async` function still returns a promise to its caller.

## 10. Advanced Notes
- `async/await` compiles down to promises and the microtask queue.
- `for await...of` consumes async iterators/streams.
- Mixing `await` inside `Array.map` returns an array of promises — wrap with `Promise.all`.
- Sequential vs parallel matters for performance; profile awaited calls in hot paths.
