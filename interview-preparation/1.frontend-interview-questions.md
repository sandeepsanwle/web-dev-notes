# Frontend Interview Questions

> A curated bank of frontend interview questions **with concise answers**, grouped by topic. Each question has a difficulty tag: 🟢 Easy · 🟡 Medium · 🔴 Hard.
>
> Use this to drill fundamentals quickly. Read the question, try to answer out loud, then check the answer.

**Legend:** 🟢 Junior-friendly · 🟡 Mid-level · 🔴 Senior / tricky

---

## HTML/CSS

**Q:** What is the difference between `block`, `inline`, and `inline-block` elements? 🟢
**A:** `block` elements take the full width and start on a new line (e.g. `<div>`, `<p>`). `inline` elements flow within text and ignore width/height (e.g. `<span>`, `<a>`). `inline-block` flows inline but **respects** width, height, and vertical margins/padding.

**Q:** Explain the CSS box model. 🟢
**A:** Every element is a box made of (inside-out): **content → padding → border → margin**. By default `box-sizing: content-box` means width/height apply to content only. Setting `box-sizing: border-box` makes width/height include padding and border, which is easier to reason about.

**Q:** What is the difference between `position: relative`, `absolute`, `fixed`, and `sticky`? 🟡
**A:**
- `relative`: positioned relative to its normal spot; still occupies space.
- `absolute`: removed from flow, positioned relative to nearest positioned ancestor.
- `fixed`: positioned relative to the viewport; stays on scroll.
- `sticky`: behaves like `relative` until a scroll threshold, then sticks like `fixed`.

**Q:** How does CSS specificity work? 🟡
**A:** Specificity decides which rule wins. Weight order (high → low): inline styles > IDs > classes/attributes/pseudo-classes > elements/pseudo-elements. `!important` overrides normal specificity. When equal, the **last** declared rule wins.

**Q:** Flexbox vs CSS Grid — when to use which? 🟡
**A:** **Flexbox** is one-dimensional (a row *or* a column) — great for toolbars, nav, distributing items along an axis. **Grid** is two-dimensional (rows *and* columns simultaneously) — great for page layouts and complex card grids. They compose well together.

**Q:** What are semantic HTML elements and why do they matter? 🟢
**A:** Semantic tags (`<header>`, `<nav>`, `<main>`, `<article>`, `<section>`, `<footer>`) describe meaning, not just appearance. Benefits: better accessibility (screen readers), SEO, and more readable, maintainable markup.

**Q:** How do you make a website responsive? 🟢
**A:** Use a fluid layout (%, `fr`, `flex`), `max-width` instead of fixed `width`, relative units (`rem`, `em`, `vw`), media queries for breakpoints, responsive images (`srcset`/`<picture>`), and the meta viewport tag: `<meta name="viewport" content="width=device-width, initial-scale=1">`.

**Q:** What's the difference between `em`, `rem`, `px`, `%`, and `vh/vw`? 🟡
**A:** `px` is absolute. `em` is relative to the **parent's** font size (compounds). `rem` is relative to the **root** font size (predictable). `%` is relative to the parent's corresponding property. `vh`/`vw` are 1% of the viewport height/width.

---

## JavaScript

**Q:** What is the difference between `var`, `let`, and `const`? 🟢
**A:** `var` is function-scoped and hoisted (initialized as `undefined`). `let` and `const` are block-scoped and live in the "temporal dead zone" until declared. `const` cannot be **reassigned** (but objects it points to can still be mutated).

**Q:** Explain `==` vs `===`. 🟢
**A:** `==` is loose equality and performs type coercion (`0 == "0"` is `true`). `===` is strict equality with no coercion (`0 === "0"` is `false`). Prefer `===` to avoid surprises.

**Q:** What is a closure? 🟡
**A:** A closure is a function that "remembers" the variables from the scope where it was created, even after that scope has finished executing. Used for data privacy, factories, and callbacks.

```js
function counter() {
  let count = 0;
  return () => ++count; // closes over `count`
}
const next = counter();
next(); // 1
next(); // 2
```

**Q:** Explain the event loop, call stack, microtasks, and macrotasks. 🔴
**A:** JS is single-threaded. The **call stack** runs synchronous code. Async callbacks queue up: **microtasks** (Promises, `queueMicrotask`) and **macrotasks** (`setTimeout`, I/O). After each stack-clearing tick, the event loop drains **all microtasks first**, then one macrotask, and repeats. That's why a resolved Promise's `.then` runs before a `setTimeout(0)`.

**Q:** What does `this` refer to in JavaScript? 🔴
**A:** It depends on the call site: in a method call it's the object before the dot; standalone it's the global object (or `undefined` in strict mode); with `new` it's the new instance; with `call`/`apply`/`bind` it's the explicitly bound value. **Arrow functions** have no own `this` — they inherit it from the enclosing scope.

**Q:** Difference between `null` and `undefined`? 🟢
**A:** `undefined` means a variable was declared but never assigned (or a missing property). `null` is an intentional "no value" you assign explicitly. `typeof undefined === "undefined"`, but `typeof null === "object"` (a historic quirk).

**Q:** What is the difference between `map`, `forEach`, `filter`, and `reduce`? 🟢
**A:** `forEach` runs a side-effect, returns nothing. `map` returns a new array of transformed values. `filter` returns a new array of items passing a test. `reduce` folds the array into a single accumulated value.

**Q:** Explain `Promise`, `async/await`, and how to handle errors. 🟡
**A:** A `Promise` represents an eventual value (pending → fulfilled/rejected). `async/await` is syntactic sugar that lets you write async code like synchronous code. Handle errors with `try/catch` around `await`, or `.catch()` on the promise chain.

**Q:** What is debouncing vs throttling? 🟡
**A:** **Debounce** waits until activity stops for N ms before firing (good for search-as-you-type). **Throttle** fires at most once every N ms regardless of how often it's triggered (good for scroll/resize handlers).

**Q (output prediction):** What does this log? 🟡

```js
console.log(1);
setTimeout(() => console.log(2), 0);
Promise.resolve().then(() => console.log(3));
console.log(4);
```

**A:** `1, 4, 3, 2`. Synchronous logs (`1`, `4`) run first. The microtask (`3`) runs before the macrotask `setTimeout` (`2`).

---

## TypeScript

**Q:** What problem does TypeScript solve? 🟢
**A:** It adds optional **static typing** on top of JavaScript, catching type errors at compile time, enabling better autocomplete/refactoring, and serving as self-documenting code. It compiles down to plain JS.

**Q:** What is the difference between `interface` and `type`? 🟡
**A:** Both describe object shapes. `interface` can be **merged** (declaration merging) and is idiomatic for object/class contracts. `type` is more flexible — it can represent unions, intersections, primitives, tuples, and mapped types. Rule of thumb: `interface` for public object shapes, `type` for unions/utility types.

**Q:** What are generics and why use them? 🟡
**A:** Generics let you write reusable, type-safe code that works over many types without losing type info.

```ts
function identity<T>(value: T): T {
  return value;
}
const n = identity<number>(5); // n is number
```

**Q:** Explain `any`, `unknown`, `never`, and `void`. 🔴
**A:** `any` opts out of type checking (avoid). `unknown` is the type-safe `any` — you must narrow it before use. `never` represents values that never occur (e.g. a function that always throws). `void` is the absence of a return value.

**Q:** What are union and intersection types? 🟡
**A:** A **union** (`A | B`) means a value is one of several types. An **intersection** (`A & B`) means a value has **all** the members of the combined types.

**Q:** What are utility types like `Partial`, `Pick`, `Omit`, `Record`? 🟡
**A:** Built-in type transformers: `Partial<T>` makes all props optional, `Required<T>` makes them required, `Pick<T, K>` selects keys, `Omit<T, K>` removes keys, `Record<K, V>` builds an object type with keys `K` and values `V`.

**Q:** What is type narrowing? 🟡
**A:** Refining a broad type to a more specific one using runtime checks like `typeof`, `instanceof`, `in`, equality checks, or custom **type guards** (`function isCat(a: Animal): a is Cat`). The compiler then knows the narrower type within that block.

---

## React

**Q:** What is the virtual DOM and how does reconciliation work? 🟡
**A:** The virtual DOM is a lightweight in-memory representation of the UI. On state change, React builds a new tree, **diffs** it against the previous one (reconciliation), and applies the minimal set of real DOM updates. Keys help it match list items efficiently.

**Q:** What are the rules of hooks? 🟡
**A:** (1) Only call hooks at the **top level** — not inside loops, conditions, or nested functions. (2) Only call hooks from React function components or custom hooks. This guarantees consistent hook call order across renders.

**Q:** Explain `useState` vs `useReducer`. 🟡
**A:** `useState` is for simple, independent pieces of state. `useReducer` centralizes complex state logic with a reducer `(state, action) => newState` — better when state transitions are interrelated or numerous.

**Q:** What does `useEffect` do and when does it run? 🟡
**A:** It runs side effects (data fetching, subscriptions, DOM mutations) after render. The dependency array controls when: `[]` runs once on mount; `[a, b]` runs when `a` or `b` change; no array runs after every render. Return a cleanup function to undo effects (e.g. unsubscribe).

**Q:** What is the difference between `useMemo` and `useCallback`? 🔴
**A:** `useMemo` memoizes a **computed value**; `useCallback` memoizes a **function reference**. `useCallback(fn, deps)` is equivalent to `useMemo(() => fn, deps)`. Use them to avoid expensive recomputations or unnecessary re-renders of memoized children.

**Q:** Why do React lists need a `key`? 🟢
**A:** Keys give each list item a stable identity so React can match items across renders, preserving state and minimizing DOM operations. Use stable, unique IDs — **avoid array index** when the list can reorder.

**Q:** Controlled vs uncontrolled components? 🟡
**A:** A **controlled** input has its value driven by React state (`value` + `onChange`) — single source of truth. An **uncontrolled** input keeps its own state in the DOM, read via a `ref`. Controlled is preferred for validation and dynamic UI.

**Q:** How do you avoid prop drilling? 🟡
**A:** Use the **Context API** for shared state, component composition (passing children), or a state library (Redux, Zustand, Jotai). Context is ideal for theme, auth, and locale.

**Q (output prediction):** What's wrong here and what does it log? 🔴

```jsx
function Counter() {
  const [count, setCount] = React.useState(0);
  const handleClick = () => {
    setCount(count + 1);
    setCount(count + 1);
  };
  return <button onClick={handleClick}>{count}</button>;
}
```

**A:** Clicking increments by **1**, not 2, because both `setCount` calls use the same stale `count` from this render. Fix with the functional updater: `setCount(c => c + 1)` twice, which increments by 2.

---

## Angular

**Q:** What is Angular and how does it differ from React? 🟢
**A:** Angular is a full **framework** (opinionated: routing, forms, HTTP, DI built in) using TypeScript. React is a **library** focused on the view, relying on the ecosystem for the rest. Angular uses two-way binding and decorators; React uses one-way data flow and JSX.

**Q:** Explain components, modules, and services in Angular. 🟡
**A:** A **component** controls a view (template + class + styles). A **module** (`NgModule`) groups related components/services. A **service** holds reusable logic/state and is shared via **dependency injection**.

**Q:** What is dependency injection in Angular? 🟡
**A:** DI is a design pattern where a class receives its dependencies from an external injector instead of creating them. Angular's hierarchical injector provides singletons (e.g. services) to components, improving testability and reuse.

**Q:** Explain data binding types in Angular. 🟡
**A:** Four kinds: **interpolation** `{{value}}`, **property binding** `[prop]="value"`, **event binding** `(click)="fn()"`, and **two-way binding** `[(ngModel)]="value"` (combines property + event).

**Q:** What are Observables and RxJS in Angular? 🔴
**A:** Observables are streams of async values (from `HttpClient`, events, etc.). RxJS provides operators (`map`, `filter`, `switchMap`, `debounceTime`) to transform streams declaratively. You `subscribe()` to consume, or use the `async` pipe in templates (which auto-unsubscribes).

**Q:** What is change detection in Angular? 🔴
**A:** The mechanism that syncs the model with the view. Default strategy checks the whole component tree on events/async ops. `ChangeDetectionStrategy.OnPush` only re-checks when inputs change by reference or events fire inside the component — a key performance optimization.

**Q:** What are Angular lifecycle hooks? 🟡
**A:** Methods called at component stages: `ngOnInit` (after first inputs set), `ngOnChanges` (on input change), `ngAfterViewInit` (view ready), `ngOnDestroy` (cleanup — unsubscribe here), among others.

---

## Browser/Performance

**Q:** What happens when you type a URL and press Enter? 🔴
**A:** DNS lookup → TCP/TLS handshake → HTTP request → server responds → browser parses HTML, builds the DOM and CSSOM → constructs the render tree → layout (reflow) → paint → composite. Subresources (CSS, JS, images) are fetched along the way; JS may block parsing.

**Q:** How do you improve web page load performance? 🟡
**A:** Minify/compress assets (gzip/brotli), code-split and lazy-load, optimize and lazy-load images, cache (HTTP caching, CDN), reduce render-blocking CSS/JS (`defer`/`async`), use HTTP/2, and minimize main-thread work.

**Q:** What is the Critical Rendering Path? 🔴
**A:** The sequence the browser must complete to render initial content: parse HTML → DOM, parse CSS → CSSOM, combine into render tree, layout, paint. Optimizing it (inlining critical CSS, deferring JS) speeds up first paint.

**Q:** Explain browser storage options: cookies, localStorage, sessionStorage. 🟡
**A:** **Cookies** (~4KB) sent with every request, can be httpOnly/secure — good for auth tokens. **localStorage** (~5–10MB) persists until cleared, not sent to server. **sessionStorage** is like localStorage but cleared when the tab closes.

**Q:** What is reflow vs repaint? 🔴
**A:** **Reflow (layout)** recalculates element geometry — expensive, triggered by size/position changes. **Repaint** redraws pixels without layout changes (e.g. color change). Batch DOM reads/writes and animate `transform`/`opacity` (composited) to avoid layout thrashing.

**Q:** What are `defer` and `async` on script tags? 🟡
**A:** Both download the script without blocking HTML parsing. `async` executes as soon as it's downloaded (order not guaranteed). `defer` executes after parsing completes, in document order. Use `defer` for dependent scripts.

**Q:** What is lazy loading and how do you implement it? 🟢
**A:** Deferring loading of resources until needed. Images: `<img loading="lazy">`. Components/routes: dynamic `import()` with React `lazy`/`Suspense` or Angular lazy routes. Reduces initial bundle size and load time.

---

## Web Security

**Q:** What is XSS and how do you prevent it? 🔴
**A:** **Cross-Site Scripting** injects malicious scripts into pages viewed by others. Prevent by escaping/encoding output, avoiding `innerHTML`/`dangerouslySetInnerHTML` with untrusted data, sanitizing input, and using a **Content Security Policy (CSP)**.

**Q:** What is CSRF and how do you prevent it? 🔴
**A:** **Cross-Site Request Forgery** tricks an authenticated user's browser into sending unwanted requests. Prevent with anti-CSRF tokens, `SameSite` cookies, and verifying the `Origin`/`Referer` header for state-changing requests.

**Q:** What is CORS? 🟡
**A:** **Cross-Origin Resource Sharing** is a browser security mechanism that controls which origins can call an API. The server sends `Access-Control-Allow-Origin` (and related) headers; the browser blocks disallowed cross-origin responses. It's enforced by the browser, not the server.

**Q:** Why use HTTPS? 🟢
**A:** HTTPS encrypts traffic (TLS), preventing eavesdropping and tampering (man-in-the-middle attacks), and authenticates the server's identity. It's also required for many modern browser APIs (service workers, geolocation).

**Q:** How should you store authentication tokens on the client? 🔴
**A:** Prefer **httpOnly, Secure, SameSite cookies** so JS can't read the token (mitigates XSS theft). `localStorage` is convenient but vulnerable to XSS. Whatever you choose, keep tokens short-lived and pair with refresh tokens.

**Q:** What is the principle of least privilege and content security policy? 🟡
**A:** **Least privilege**: grant the minimum access needed. **CSP** is an HTTP header that restricts which sources scripts/styles/images can load from, drastically reducing XSS impact (e.g. `Content-Security-Policy: default-src 'self'`).

**Q:** What is clickjacking and how do you prevent it? 🟡
**A:** Tricking users into clicking hidden UI via a transparent iframe overlay. Prevent with the `X-Frame-Options: DENY` header (or CSP `frame-ancestors 'none'`) to disallow your site being framed.

---

### How to use this file
- Practice answering **out loud** in 30–60 seconds per question.
- For 🔴 questions, be ready to draw a diagram or write a code snippet.
- Pair this with `system-design-basics.md` for senior-level rounds.
