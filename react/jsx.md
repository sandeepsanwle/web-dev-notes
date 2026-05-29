# JSX

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

JSX (JavaScript XML) is a syntax extension for JavaScript that lets you write HTML-like markup directly inside your JavaScript code. React uses it to describe what the UI should look like.

## 2. Simple Explanation

Instead of building UI with verbose `React.createElement()` calls, JSX lets you write tags that *look* like HTML. A build tool (Babel) compiles that markup back into plain JavaScript function calls that the browser understands.

So this:

```jsx
const element = <h1>Hello, world!</h1>;
```

is just sugar for:

```jsx
const element = React.createElement("h1", null, "Hello, world!");
```

## 3. Why It Is Used

- Makes UI code **readable** and **declarative** — markup and logic live together.
- Catches errors at compile time (mismatched tags, invalid attributes).
- Lets you embed dynamic values and JavaScript expressions inside markup.
- Keeps rendering logic close to the data it depends on.

## 4. Key Points

- JSX is **not** HTML; it compiles to `React.createElement` calls.
- Use `className` instead of `class`, and `htmlFor` instead of `for`.
- Attributes and event handlers are **camelCase** (`onClick`, `tabIndex`).
- A component must return a **single root element** (use a Fragment `<>...</>` to avoid extra DOM nodes).
- Embed JavaScript expressions with curly braces: `{expression}`.
- JSX expressions must be expressions, not statements (no `if`/`for` directly — use ternaries or `.map()`).

## 5. Syntax

```jsx
// Embedding expressions
const name = "Ada";
const greeting = <p>Hello, {name}!</p>;

// Attributes
const img = <img src={user.avatarUrl} alt={user.name} className="avatar" />;

// Multiple elements need one parent (Fragment)
function List() {
  return (
    <>
      <li>One</li>
      <li>Two</li>
    </>
  );
}
```

## 6. Example

```jsx
function ProfileCard({ user }) {
  const isOnline = user.status === "active";

  return (
    <div className="card">
      <img src={user.avatar} alt={user.name} />
      <h2>{user.name}</h2>
      {/* Conditional rendering with a ternary */}
      <span>{isOnline ? "🟢 Online" : "⚪ Offline"}</span>

      {/* Rendering a list */}
      <ul>
        {user.skills.map((skill) => (
          <li key={skill}>{skill}</li>
        ))}
      </ul>
    </div>
  );
}
```

## 7. Real World Use Case

Every screen in a React app — a dashboard, a product page, a chat window — is built from JSX. For example, an e-commerce product grid maps over an array of products and renders a `<ProductCard />` for each, embedding price, image, and a "Add to Cart" button, all expressed cleanly in JSX.

## 8. Interview Questions

**Q1:** What is JSX and why can't the browser run it directly?
**A:** JSX is a syntax extension that lets you write HTML-like markup in JavaScript. Browsers don't understand it natively; a transpiler like Babel converts it into `React.createElement()` calls (or the automatic JSX runtime's `_jsx()` calls) before it runs.

**Q2:** Why must a component return a single root element, and how do you return multiple siblings?
**A:** Because JSX compiles to a single function call that returns one element/object. To return siblings without adding an extra DOM wrapper, wrap them in a Fragment (`<>...</>` or `<React.Fragment>`).

**Q3:** Why is `key` required when rendering lists in JSX?
**A:** `key` gives each list item a stable identity so React's reconciliation can efficiently track which items were added, removed, or reordered. Keys should be stable and unique among siblings — avoid using array indexes when the list can change.

**Q4:** Why does JSX use `className` and `htmlFor` instead of `class` and `for`?
**A:** Because `class` and `for` are reserved words in JavaScript. JSX maps to DOM properties, which use `className` and `htmlFor`.

**Q5:** How do you write conditional content in JSX?
**A:** Use a ternary (`{cond ? <A/> : <B/>}`), logical AND short-circuiting (`{cond && <A/>}`), or compute the element in a variable before the `return`. You cannot use `if` statements directly inside JSX.

## 9. Common Mistakes

- Using `class` instead of `className`.
- Forgetting `key` on list items, or using the array index as a key on a dynamic list.
- Returning multiple sibling elements without a parent/Fragment.
- Writing `{if (x) {...}}` inside JSX — only expressions are allowed.
- Using `&&` with a number: `{count && <X/>}` renders `0` when count is 0. Use `count > 0 && <X/>`.

## 10. Advanced Notes

- Since React 17, the **automatic JSX runtime** means you no longer need `import React from "react"` just to use JSX; the compiler injects `jsx`/`jsxs` from `react/jsx-runtime`.
- JSX is configurable: tools like Preact or Emotion use a custom `pragma` (e.g. `/** @jsxImportSource @emotion/react */`).
- JSX evaluates expressions eagerly — putting heavy computation directly in JSX runs it on every render. Memoize or hoist it out.
- JSX whitespace and newlines are collapsed similarly to HTML, which can surprise you when formatting text nodes.
