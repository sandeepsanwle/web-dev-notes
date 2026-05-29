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
| JavaScript Event Loop phases | `javascript/event-loop.md`, `nodejs/event-loop.md` |
| Prototype chain | `javascript/prototype.md` |
| Scope chain & closures | `javascript/scope.md`, `javascript/closures.md` |
| React Virtual DOM diffing | `react/virtual-dom.md`, `react/reconciliation.md` |
| Request/middleware pipeline | `expressjs/middleware.md` |
| System design building blocks | `interview-preparation/system-design-basics.md` |

> 💡 Many notes use inline **Mermaid** and ASCII diagrams (rendered by GitHub) so they need no image files. Add binary diagrams here only when they add real value.
