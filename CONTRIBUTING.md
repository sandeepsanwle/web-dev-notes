# 🤝 Contributing to Web Dev Notes

Thanks for your interest in improving **web-dev-notes**! This is a community-driven, open-source learning resource and contributions of all sizes are welcome.

## Ways to Contribute

- ✍️ Add a **new topic** following the standard template.
- 🛠️ **Fix** errors, typos, or outdated information.
- 💡 Improve **examples**, add diagrams, or clarify explanations.
- ➕ Add more **interview questions** to existing files.

## The Note Template

Every topic file **must** follow this exact 10-section structure:

```text
# Topic Name

> **Difficulty:** 🟢 Beginner | 🟡 Intermediate | 🔴 Advanced

## 1. Basic Definition
## 2. Simple Explanation
## 3. Why It Is Used
## 4. Key Points
## 5. Syntax
## 6. Example
## 7. Real World Use Case
## 8. Interview Questions      (exactly 5 Q&A)
## 9. Common Mistakes
## 10. Advanced Notes
```

## Workflow

1. **Fork** the repository.
2. Create a branch: `git checkout -b add/<topic-name>`.
3. Add or edit markdown files. Use **kebab-case** filenames (e.g. `async-await.md`).
4. If you add a topic, link it in the relevant folder's `README.md` index **and** the progress checklist in the root `README.md`.
5. **Commit** with a clear message: `docs: add notes on <topic>`.
6. **Push** and open a **Pull Request**.

## Style Guide

| Rule | Detail |
| :--- | :--- |
| Formatting | GitHub-flavored Markdown |
| Code blocks | Always fenced with a language tag (` ```js `, ` ```ts `, ` ```tsx `) |
| Tone | Beginner-friendly, but interview-oriented |
| Examples | Short, correct, and runnable |
| Tables | Use for comparisons and references |
| Diagrams | Prefer inline Mermaid/ASCII; add images to `/assets` only when needed |

## Code of Conduct

Be respectful, constructive, and welcoming. We're all here to learn. 🌱
