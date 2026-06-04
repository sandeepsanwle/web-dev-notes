# State

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

State is internal, mutable data that a component owns and manages. When state changes, React re-renders the component to reflect the new data on screen.

## 2. Simple Explanation

State is a component's memory. A counter remembers its count; a form remembers what you typed; a toggle remembers if it's on or off. When you update state with the setter function, React automatically re-draws the UI.

## 3. Why It Is Used

- Track data that **changes over time** (input values, counters, toggles, fetched data).
- Make UIs **interactive** and reactive to user actions.
- Trigger **re-renders** automatically when data updates.
- Keep the displayed UI in sync with the underlying data.

## 4. Key Points

- In function components, use the **`useState`** Hook.
- **Never mutate state directly** — always use the setter (`setCount(...)`).
- State updates are **asynchronous** and may be **batched**.
- Use the **functional updater** `setX(prev => ...)` when the new value depends on the old.
- State updates trigger a **re-render** of that component and its children.
- For complex state logic, prefer **`useReducer`**.

## 5. Syntax

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0); // [value, setter], initial value 0

  return (
    <button onClick={() => setCount((prev) => prev + 1)}>
      Count: {count}
    </button>
  );
}
```

## 6. Example

```jsx
import { useState } from "react";

function SignupForm() {
  const [form, setForm] = useState({ email: "", agreed: false });

  const update = (field, value) =>
    setForm((prev) => ({ ...prev, [field]: value })); // immutable update

  return (
    <form>
      <input
        value={form.email}
        onChange={(e) => update("email", e.target.value)}
      />
      <label>
        <input
          type="checkbox"
          checked={form.agreed}
          onChange={(e) => update("agreed", e.target.checked)}
        />
        I agree
      </label>
      <button disabled={!form.agreed}>Sign up</button>
    </form>
  );
}
```

## 7. Real World Use Case

A shopping cart uses state to track items, quantities, and the total price. Clicking "Add to Cart" updates the cart state, which instantly re-renders the cart badge count and totals across the page.

## 8. Interview Questions

**Q1:** What is state and how is it different from props?
**A:** State is internal data owned and mutated by a component; props are external, read-only data passed in from a parent. State changes trigger re-renders of the owning component.

**Q2:** Why shouldn't you mutate state directly?
**A:** React detects changes by comparing references. Mutating state in place (e.g. `state.x = 1`) keeps the same reference, so React may skip the re-render. Always create a new object/array via the setter so React sees the change.

**Q3:** Why are state updates asynchronous, and how do you read the latest value?
**A:** React batches updates for performance, so the state variable isn't updated immediately within the same event handler. To compute a new value from the previous one reliably, use the functional updater form `setX(prev => prev + 1)`.

**Q4:** What happens when you call a state setter with the same value?
**A:** If the new value is identical (via `Object.is`) to the current one, React may bail out and skip re-rendering that component, avoiding wasted work.

**Q5:** When should you use `useReducer` instead of `useState`?
**A:** Use `useReducer` when state logic is complex, involves multiple sub-values, or the next state depends on intricate transitions. It centralizes update logic in a reducer function, making it more predictable and testable.

## 9. Common Mistakes

- Mutating state directly (`arr.push(x)`, `obj.key = v`) instead of creating new copies.
- Expecting state to update synchronously right after calling the setter.
- Using stale state in updates instead of the functional updater form.
- Storing derived data in state instead of computing it during render.
- Putting too much in one state object when separate `useState` calls would be clearer.

## 10. Advanced Notes

- React **batches** multiple state updates in event handlers (and, in React 18, in promises/timeouts too) into a single re-render.
- **Lifting state up**: move shared state to the closest common ancestor when multiple components need it.
- Lazy initialization: `useState(() => expensiveInit())` runs the initializer only once.
- For server data, prefer dedicated tools (React Query/SWR) over manual state — they handle caching, refetching, and loading/error states.
- State is preserved by component **position** in the tree; changing keys or conditionally rendering can reset it.
