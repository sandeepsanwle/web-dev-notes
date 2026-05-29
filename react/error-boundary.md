# Error Boundary

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

An error boundary is a special React component that **catches JavaScript errors** anywhere in its child component tree during rendering, in lifecycle methods, and in constructors — then displays a fallback UI instead of crashing the whole app.

## 2. Simple Explanation

Without protection, one component throwing an error can unmount the entire React app, leaving a blank screen. An error boundary acts like a safety net around part of the UI: if something inside breaks, it shows a friendly "Something went wrong" message and keeps the rest of the app alive.

## 3. Why It Is Used

- Prevent a single component crash from breaking the **whole app**.
- Show a graceful **fallback UI** when errors occur.
- **Log** errors to monitoring services (Sentry, etc.).
- Isolate risky parts of the UI (widgets, third-party components).

## 4. Key Points

- Error boundaries must be **class components** (using `getDerivedStateFromError` and/or `componentDidCatch`).
- `static getDerivedStateFromError` updates state to render the fallback.
- `componentDidCatch(error, info)` is for **side effects** like logging.
- They catch errors in **rendering, lifecycle methods, and constructors** of descendants.
- They do **NOT** catch: event handlers, async code (setTimeout/fetch), SSR, or errors thrown in the boundary itself.
- For function components, use a library like `react-error-boundary`.

## 5. Syntax

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true }; // render fallback next
  }

  componentDidCatch(error, info) {
    logToService(error, info.componentStack); // side effect
  }

  render() {
    if (this.state.hasError) return <h1>Something went wrong.</h1>;
    return this.props.children;
  }
}
```

## 6. Example

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, info) {
    console.error("Caught:", error, info.componentStack);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div role="alert">
          <p>Oops, this section failed to load.</p>
          <button onClick={() => this.setState({ hasError: false })}>
            Try again
          </button>
        </div>
      );
    }
    return this.props.children;
  }
}

// Usage: isolate a risky widget
function App() {
  return (
    <ErrorBoundary>
      <Dashboard />
    </ErrorBoundary>
  );
}
```

## 7. Real World Use Case

A dashboard composed of independent widgets (charts, feeds, third-party embeds) wraps each widget in its own error boundary. If the "Stock Chart" widget throws because of bad data, only that widget shows "Failed to load chart," while the rest of the dashboard keeps working — the user isn't kicked to a blank page.

## 8. Interview Questions

**Q1:** What is an error boundary and what does it catch?
**A:** It's a component that catches JavaScript errors in its child tree during rendering, lifecycle methods, and constructors, and renders a fallback UI instead of crashing the app. It does not catch errors in event handlers, async code, or SSR.

**Q2:** Why can't error boundaries be written as function components?
**A:** They rely on the class-only methods `static getDerivedStateFromError` (to render fallback) and `componentDidCatch` (to log). React has no Hook equivalent yet, so you either write a class or use a library like `react-error-boundary` that wraps one.

**Q3:** What errors do error boundaries NOT catch, and how do you handle those?
**A:** They don't catch errors in event handlers, asynchronous code (`setTimeout`, promises, `fetch`), server-side rendering, or thrown inside the boundary itself. Handle those with regular `try/catch` and state updates within the handler/async function.

**Q4:** What's the difference between `getDerivedStateFromError` and `componentDidCatch`?
**A:** `getDerivedStateFromError` is a static method that runs during the render phase and returns new state to show the fallback (no side effects allowed). `componentDidCatch` runs in the commit phase and is where you perform side effects like logging the error and component stack.

**Q5:** Where should you place error boundaries in an app?
**A:** Strategically: a top-level boundary for a global fallback, plus granular boundaries around independent or risky sections (widgets, routes, third-party components) so a failure in one area doesn't take down the whole UI.

## 9. Common Mistakes

- Expecting boundaries to catch event-handler or async errors (they don't).
- Writing only one global boundary, so any error blanks the entire app section.
- Putting the throwing logic inside the boundary component itself (it won't catch its own errors).
- Forgetting to log errors for observability.
- Not providing a way to recover/retry from the fallback UI.

## 10. Advanced Notes

- **`react-error-boundary`** provides a reusable `<ErrorBoundary>` with `FallbackComponent`, `onReset`, and a `useErrorBoundary` Hook to trigger boundaries from async code.
- Combine with **`<Suspense>`**: Suspense handles loading states, error boundaries handle failures (including failed lazy-chunk loads).
- Reset boundaries by changing a `resetKeys` prop or remounting, so users can retry after fixing the cause.
- In development, React still shows the error overlay even when a boundary catches it; in production, only the fallback appears.
- Pair boundaries with monitoring (Sentry/Datadog) using the `componentStack` for rich diagnostics.
