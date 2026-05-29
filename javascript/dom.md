# DOM

> **Difficulty:** 🟡 Intermediate

## 1. Basic Definition
The DOM (Document Object Model) is a programming interface that represents an HTML/XML document as a tree of nodes (elements, text, attributes). JavaScript uses the DOM API to read, modify, add, and remove page content and respond to user events.

## 2. Simple Explanation
The DOM is a live, tree-shaped model of your web page that JavaScript can touch. Each tag becomes a node you can grab and change, so the page updates without a reload.

## 3. Why It Is Used
The DOM is how JavaScript makes pages interactive — updating text, styling elements, handling clicks and input, and dynamically building UI in response to data or user actions.

## 4. Key Points
- Select elements: `getElementById`, `querySelector`, `querySelectorAll`.
- Modify: `textContent`, `innerHTML`, `setAttribute`, `classList`, `style`.
- Create/insert: `createElement`, `append`, `appendChild`, `remove`.
- Events: `addEventListener`, event bubbling/capturing, `event.target`.
- Reflows/repaints are costly — batch DOM updates for performance.

## 5. Syntax
```js
const el = document.querySelector(".box");
el.textContent = "Hello";
el.classList.add("active");
el.addEventListener("click", (e) => {
  console.log(e.target);
});
```

## 6. Example
```js
const list = document.querySelector("#list");

["Apple", "Banana"].forEach((fruit) => {
  const li = document.createElement("li");
  li.textContent = fruit;
  list.appendChild(li);
});

list.addEventListener("click", (e) => {
  if (e.target.tagName === "LI") {
    e.target.classList.toggle("done"); // event delegation
  }
});
```
We build list items dynamically and use a single delegated listener on the parent for all items.

## 7. Real World Use Case
Rendering dynamic content, form validation and feedback, interactive widgets (modals, dropdowns), single-page app updates, and attaching analytics or behavior to user interactions.

## 8. Interview Questions
**Q1:** What is the DOM?
**A:** A tree-structured object representation of an HTML document that JavaScript can read and manipulate via an API.

**Q2:** Difference between `innerHTML` and `textContent`?
**A:** `innerHTML` parses and sets HTML markup (XSS risk); `textContent` sets plain text safely and is faster.

**Q3:** What is event delegation?
**A:** Attaching one listener to a parent and using `event.target` to handle events from many child elements, leveraging bubbling.

**Q4:** Difference between event bubbling and capturing?
**A:** Bubbling propagates from target up to ancestors; capturing goes from the root down to the target. `addEventListener`'s third arg toggles the phase.

**Q5:** Why can frequent DOM manipulation be slow?
**A:** Each change can trigger reflow/repaint; batching updates or using `DocumentFragment`/virtual DOM reduces cost.

## 9. Common Mistakes
- Using `innerHTML` with untrusted input (XSS vulnerability).
- Querying the DOM inside loops repeatedly instead of caching references.
- Adding many listeners instead of delegating.
- Manipulating elements before the DOM has loaded.

## 10. Advanced Notes
- `DocumentFragment` batches insertions to minimize reflows.
- `MutationObserver` watches DOM changes; `IntersectionObserver` detects visibility.
- `requestAnimationFrame` schedules visual updates aligned with paint.
- Virtual DOM (React) diffs an in-memory tree to minimize real DOM operations.

### DOM tree diagram
```
document
  └─ html
       ├─ head
       └─ body
            └─ ul#list
                 ├─ li "Apple"
                 └─ li "Banana"
```
