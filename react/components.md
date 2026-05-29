# Components

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

A component is an independent, reusable piece of UI. In modern React, a component is a JavaScript function that accepts inputs (props) and returns JSX describing what should appear on screen.

## 2. Simple Explanation

Think of components like LEGO bricks. You build small pieces (`Button`, `Avatar`, `Input`), then snap them together into bigger pieces (`LoginForm`, `Navbar`), and finally into a whole page (`App`). Each brick manages its own look and behavior.

## 3. Why It Is Used

- **Reusability** — write once, use everywhere.
- **Separation of concerns** — each component owns one responsibility.
- **Maintainability** — fix or restyle a piece in one place.
- **Composability** — combine small components into complex UIs.
- **Testability** — components are easy to test in isolation.

## 4. Key Points

- Component names **must start with a capital letter** (`MyButton`, not `myButton`) so React treats them as components, not DOM tags.
- Function components are the modern standard; class components are legacy but still valid.
- A component must return JSX (or `null` to render nothing).
- Components can be **nested** and **composed** through props and `children`.
- Keep components **pure**: given the same props, return the same output without side effects during render.

## 5. Syntax

```jsx
// Function component (modern, preferred)
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

// Arrow function variant
const Welcome = ({ name }) => <h1>Hello, {name}</h1>;

// Class component (legacy)
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

## 6. Example

```jsx
function Avatar({ src, alt }) {
  return <img className="avatar" src={src} alt={alt} />;
}

function UserInfo({ user }) {
  return (
    <div className="user-info">
      <Avatar src={user.avatar} alt={user.name} />
      <span>{user.name}</span>
    </div>
  );
}

// Composition with children
function Card({ children }) {
  return <div className="card">{children}</div>;
}

function App() {
  const user = { name: "Ada", avatar: "/ada.png" };
  return (
    <Card>
      <UserInfo user={user} />
    </Card>
  );
}
```

## 7. Real World Use Case

A design system (like Material UI or a company's internal library) is a collection of components: `Button`, `Modal`, `Table`, `Tabs`. Product teams compose these to build entire apps quickly and consistently, ensuring the same look and accessibility everywhere.

## 8. Interview Questions

**Q1:** What is the difference between a function component and a class component?
**A:** Function components are plain functions that return JSX and use Hooks for state/lifecycle; they're concise and the current standard. Class components extend `React.Component`, use `this.state`, `setState`, and lifecycle methods. Function components are preferred for new code.

**Q2:** Why must component names start with a capital letter?
**A:** JSX treats lowercase tags as built-in DOM elements (strings like `"div"`) and capitalized tags as component references. A lowercase component name would be rendered as an unknown HTML tag.

**Q3:** What does it mean for a component to be "pure"?
**A:** A pure component returns the same JSX for the same props/state and produces no side effects during rendering. Purity makes rendering predictable and enables optimizations like memoization and React's concurrent features.

**Q4:** What is the difference between a presentational (dumb) and a container (smart) component?
**A:** Presentational components focus on how things look and receive data via props. Container components focus on how things work — fetching data, holding state, and passing it down. This separation improves reusability and testing.

**Q5:** How do you share UI structure between components — what is composition?
**A:** Composition means building complex UIs by nesting components and passing JSX via the `children` prop (or render props). It's React's preferred alternative to inheritance for reuse.

## 9. Common Mistakes

- Naming a component in lowercase, so React renders it as a DOM tag.
- Defining a component **inside** another component's body — it gets recreated every render and loses state.
- Doing side effects (API calls, DOM mutations) directly in the render body instead of in `useEffect`.
- Making one giant component instead of splitting into smaller, focused ones.
- Mutating props (props are read-only).

## 10. Advanced Notes

- **`React.memo`** wraps a component to skip re-rendering when props are shallow-equal — useful for expensive pure components.
- **Higher-Order Components (HOCs)** and **render props** are older reuse patterns; custom Hooks have largely replaced them for sharing logic.
- Components should be **colocated** with their styles, tests, and types for maintainability.
- Server Components (React 18+/Next.js) run on the server and send serialized output, reducing client bundle size — they can't use state or browser-only Hooks.
