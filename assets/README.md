# 🎨 Assets

This folder holds shared resources used across the notes: diagrams, images, screenshots, and any other media referenced from markdown files.

## Guidelines

- Use **descriptive, kebab-case filenames** (e.g. `event-loop-diagram.png`).
- Prefer **SVG or PNG** for diagrams.
- Keep file sizes reasonable (compress large images).
- Reference assets from notes using **relative paths**, for example:

```markdown
![Event Loop](../assets/event-loop-diagram.png)
```

## Suggested Diagrams to Contribute

| Diagram | Used In |
| :--- | :--- |
| JavaScript Event Loop phases | `javascript/9.event-loop.md`, `nodejs/1.event-loop.md` |
| Microtask vs macrotask queues | `javascript/10.micro-macro-task-queue.md` |
| Prototype chain | `javascript/14.prototype.md` |
| Scope chain & closures | `javascript/4.scope.md`, `javascript/5.closures.md` |
| React Virtual DOM diffing | `react/12.virtual-dom.md`, `react/13.reconciliation.md` |
| Request/middleware pipeline | `expressjs/2.middleware.md` |
| System design building blocks | `interview-preparation/3.system-design-basics.md` |

> 💡 Many notes use inline **Mermaid** and ASCII diagrams (rendered by GitHub) so they need no image files. Add binary diagrams here only when they add real value.
