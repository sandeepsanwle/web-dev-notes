# Reconciliation

> **Difficulty:** 🔴 Advanced

## 1. Basic Definition

Reconciliation is React's process of **diffing** the new Virtual DOM tree against the previous one to determine the minimal set of changes needed, then updating the real DOM accordingly.

## 2. Simple Explanation

When state changes, React produces a new virtual tree. Reconciliation is the smart comparison that asks "what's actually different?" Instead of rebuilding everything, React figures out which nodes to add, update, move, or remove — and only touches those parts of the real DOM.

## 3. Why It Is Used

- Update the DOM **efficiently** by changing only what differs.
- Avoid re-creating and re-rendering unchanged parts of the tree.
- Make the declarative model performant at scale.
- Correctly preserve component **state** and DOM identity across renders.

## 4. Key Points

- React uses **heuristics** to keep diffing near O(n) instead of O(n³).
- **Two main assumptions:** (1) different element **types** produce different trees; (2) **keys** identify which child elements are stable across renders.
- Same type → React **updates** the existing node's props/attributes.
- Different type → React **tears down** the old subtree and builds a new one (state is lost).
- **Keys** let React match list items across renders to reorder instead of recreate.
- Powered by the **Fiber** architecture (interruptible, prioritized work).

## 5. Syntax

```jsx
// Keys drive list reconciliation — must be stable & unique among siblings
{items.map((item) => (
  <Row key={item.id} data={item} /> // ✅ stable id
))}

// ❌ Anti-pattern: index keys break reconciliation when the list reorders
{items.map((item, i) => <Row key={i} data={item} />)}
```

## 6. Example

```jsx
// Changing element TYPE destroys the subtree and its state.
function Toggle({ asButton }) {
  // Switching between <button> and <a> unmounts one and mounts the other,
  // so any internal state/focus is lost during reconciliation.
  return asButton ? (
    <button className="cta">Click</button>
  ) : (
    <a className="cta" href="#">Click</a>
  );
}

// Keeping the SAME type but changing a prop only patches the attribute.
function Highlight({ active }) {
  // React reuses the same DOM node and just updates className.
  return <span className={active ? "on" : "off"}>Status</span>;
}
```

## 7. Real World Use Case

A sortable to-do list reorders items when the user changes the sort order. With stable `key={todo.id}` values, React's reconciler *moves* existing DOM nodes (preserving inline edit state and focus) instead of destroying and recreating them — which would happen with index keys, causing flicker and lost input.

## 8. Interview Questions

**Q1:** What is reconciliation in React?
**A:** It's the algorithm React uses to diff the newly rendered Virtual DOM against the previous one, computing the minimal set of real-DOM mutations needed to bring the UI up to date, while preserving state where possible.

**Q2:** What heuristics make React's diffing efficient?
**A:** Two key assumptions: (1) elements of different types yield different trees, so React replaces rather than diffs them deeply; (2) developers provide stable `key`s to identify children across renders. These reduce a theoretically O(n³) problem to roughly O(n).

**Q3:** Why are keys important during reconciliation?
**A:** Keys give list items a stable identity so React can match them between renders — reordering, inserting, or removing nodes precisely instead of rebuilding the list. Bad keys (like array indexes on a changing list) cause wrong state association, lost focus, and extra DOM work.

**Q4:** What happens when an element's type changes between renders?
**A:** React unmounts the old subtree entirely (discarding its state and DOM) and mounts a new one for the new type. Only when the type matches does it reuse and patch the existing node.

**Q5:** What is Fiber and how does it relate to reconciliation?
**A:** Fiber is React's reconciliation engine (since v16). It breaks rendering into small units of work that can be paused, resumed, and prioritized, enabling concurrent features like interruptible rendering and transitions — without it, large updates would block the main thread.

## 9. Common Mistakes

- Using array **index** as a key for dynamic/reorderable lists.
- Using random/unstable keys (e.g. `Math.random()`), defeating reconciliation entirely.
- Unintentionally changing an element's type/position, resetting its state.
- Assuming reconciliation deeply compares everything — it relies on type + key heuristics.
- Duplicate keys among siblings, leading to unpredictable updates.

## 10. Advanced Notes

- Fiber splits work into **render (reconciliation) phase** (interruptible, no side effects) and **commit phase** (synchronous DOM mutations).
- Priorities/lanes let urgent updates (typing) preempt non-urgent ones (data lists) via `useTransition`/`startTransition`.
- Changing a component's **position** in the tree or its **key** intentionally is a valid way to reset its state.
- React does **not** diff across different parent types — it won't try to match a moved subtree between unrelated parents.
- Understanding reconciliation explains many "why did my state reset?" and "why is my list flickering?" bugs.
