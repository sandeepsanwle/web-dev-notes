# Props

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

Props (short for "properties") are read-only inputs passed from a parent component to a child component. They let you configure and customize a component, similar to function arguments.

## 2. Simple Explanation

Props are like the settings you hand to a component. If a `Button` component can be different colors, the parent passes `color="blue"` as a prop. The child reads it but can't change it — data flows **one way**, from parent down to child.

## 3. Why It Is Used

- Pass **data** and **configuration** from parent to child.
- Make components **reusable** with different inputs.
- Pass **callbacks** so children can notify parents of events.
- Enable **composition** through the special `children` prop.

## 4. Key Points

- Props are **read-only** — never mutate them inside the child.
- Data flows **top-down** (one-way / unidirectional data flow).
- `children` is a special prop holding nested JSX.
- Use **destructuring** for cleaner code: `function X({ a, b })`.
- Provide **default values** with default parameters.
- To send data *up*, pass a function from parent to child and call it.

## 5. Syntax

```jsx
// Passing props
<Greeting name="Ada" age={30} isAdmin />

// Receiving with destructuring + defaults
function Greeting({ name, age, isAdmin = false }) {
  return <p>{name} is {age}{isAdmin ? " (admin)" : ""}</p>;
}

// Spreading props
const config = { name: "Ada", age: 30 };
<Greeting {...config} />
```

## 6. Example

```jsx
// Child receives data and a callback
function TodoItem({ text, done, onToggle }) {
  return (
    <li onClick={onToggle} style={{ textDecoration: done ? "line-through" : "none" }}>
      {text}
    </li>
  );
}

// Parent passes props down and handles events up
function TodoList() {
  const [todos, setTodos] = React.useState([
    { id: 1, text: "Learn props", done: false },
  ]);

  const toggle = (id) =>
    setTodos((prev) =>
      prev.map((t) => (t.id === id ? { ...t, done: !t.done } : t))
    );

  return (
    <ul>
      {todos.map((t) => (
        <TodoItem key={t.id} text={t.text} done={t.done} onToggle={() => toggle(t.id)} />
      ))}
    </ul>
  );
}
```

## 7. Real World Use Case

A reusable `<Button>` in a design system accepts props like `variant`, `size`, `disabled`, and `onClick`. The same component renders a primary submit button, a small danger button, or a disabled link button — all configured purely through props.

## 8. Interview Questions

**Q1:** What are props and how do they differ from state?
**A:** Props are read-only inputs passed *into* a component by its parent; the component cannot change them. State is internal, mutable data owned and managed *by* the component itself. Props flow down; state is local.

**Q2:** Why are props immutable, and what happens if you mutate them?
**A:** Props are owned by the parent, so mutating them breaks React's one-way data flow and predictability. Mutating props won't trigger a re-render and leads to bugs; to change data, the owner must update its state and pass new props down.

**Q3:** How does a child component communicate back to its parent?
**A:** The parent passes a callback function as a prop. The child calls that function (often with data) when an event occurs, letting the parent update its own state. This is "lifting state up."

**Q4:** What is the `children` prop?
**A:** `children` is a special prop containing whatever JSX is nested between a component's opening and closing tags. It enables composition, e.g. a `<Card>{content}</Card>` wrapper.

**Q5:** What is prop drilling and how can you avoid it?
**A:** Prop drilling is passing props through many intermediate components that don't use them, just to reach a deep child. You can avoid it with the Context API, component composition, or a state management library like Redux.

## 9. Common Mistakes

- Mutating props directly (e.g. `props.items.push(...)`).
- Forgetting that `{0}` and `{""}` render in JSX — guard conditional renders carefully.
- Passing a new inline object/function every render, causing unnecessary child re-renders.
- Confusing props (external, read-only) with state (internal, mutable).
- Overusing prop drilling instead of Context for deeply shared data.

## 10. Advanced Notes

- Validate props with **TypeScript** (preferred) or `PropTypes` at runtime.
- **`React.memo`** plus stable prop references (via `useMemo`/`useCallback`) prevents needless re-renders when props don't change.
- The **render props** pattern passes a function as a prop/child to share logic.
- Spreading props (`{...rest}`) is handy for wrapper components but can accidentally forward invalid DOM attributes — filter them when needed.
