# NPM

> **Difficulty:** 🟢 Beginner

## 1. Basic Definition

**NPM (Node Package Manager)** is the default package manager for Node.js. It is both a command-line tool for installing/managing dependencies and the world's largest registry of open-source JavaScript packages.

## 2. Simple Explanation

NPM is like an app store for code. Instead of writing everything yourself, you can install ready-made packages (libraries) that others have built and shared. NPM downloads them, tracks which versions you use, and lets you share your own code too.

## 3. Why It Is Used

- Install and manage **third-party dependencies** with one command.
- Track exact dependency versions so projects are **reproducible**.
- Run project **scripts** (`start`, `test`, `build`).
- **Publish** and share your own packages with the community.

## 4. Key Points

- `package.json` describes the project, dependencies, and scripts.
- `package-lock.json` locks exact versions for reproducible installs.
- Dependencies vs devDependencies: runtime vs development-only.
- **Semantic Versioning (semver)**: `MAJOR.MINOR.PATCH` with `^` and `~` ranges.
- `node_modules/` holds installed packages and should be git-ignored.

### Version Range Symbols

| Symbol | Example | Allows |
|--------|---------|--------|
| `^` | `^1.2.3` | Minor + patch updates (`<2.0.0`) |
| `~` | `~1.2.3` | Patch updates only (`<1.3.0`) |
| (none) | `1.2.3` | Exact version only |
| `*` | `*` | Any version (unsafe) |

## 5. Syntax

```bash
npm init -y                  # create package.json with defaults
npm install express          # add a runtime dependency
npm install --save-dev jest  # add a dev dependency
npm install                  # install all deps from package.json
npm uninstall express        # remove a dependency
npm run test                 # run the "test" script
npm ci                       # clean, reproducible install from lockfile
```

## 6. Example

```json
// package.json
{
  "name": "my-api",
  "version": "1.0.0",
  "main": "app.js",
  "scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.19.0"
  },
  "devDependencies": {
    "jest": "^29.7.0",
    "nodemon": "^3.1.0"
  }
}
```

```bash
# Then run:
npm install      # installs express, jest, nodemon
npm start        # runs "node app.js"
npm run dev      # runs nodemon for auto-reload
```

## 7. Real World Use Case

A team building a REST API uses `npm` to manage dependencies like `express`, `mongoose`, and `jsonwebtoken`, plus dev tools like `eslint` and `jest`. The `package-lock.json` ensures every developer and the CI/CD pipeline install the **exact same versions**, eliminating "works on my machine" bugs. CI runs `npm ci` for a clean, deterministic install before tests.

## 8. Interview Questions

**Q1:** What is the difference between `dependencies` and `devDependencies`?
**A:** `dependencies` are packages your app needs to run in production (e.g. `express`). `devDependencies` are only needed during development/testing (e.g. `jest`, `eslint`). With `npm install --production` (or `NODE_ENV=production`), devDependencies are skipped.

**Q2:** What is the purpose of `package-lock.json`?
**A:** It records the exact version, location, and integrity hash of every installed package (including nested dependencies), guaranteeing reproducible installs across machines and over time. It should be committed to version control.

**Q3:** What is the difference between `npm install` and `npm ci`?
**A:** `npm install` resolves versions from `package.json` and may update the lockfile. `npm ci` does a clean install strictly from `package-lock.json` (deleting `node_modules` first), is faster, and fails if the lockfile and `package.json` are out of sync — ideal for CI.

**Q4:** Explain semantic versioning and the `^` and `~` symbols.
**A:** Semver is `MAJOR.MINOR.PATCH`: MAJOR for breaking changes, MINOR for new backward-compatible features, PATCH for bug fixes. `^1.2.3` allows minor and patch updates (`<2.0.0`); `~1.2.3` allows only patch updates (`<1.3.0`).

**Q5:** What is the difference between local and global package installation?
**A:** Local installs (`npm install pkg`) put a package in the project's `node_modules` for use in that project. Global installs (`npm install -g pkg`) put it system-wide, typically for CLI tools (e.g. `npm i -g nodemon`). Prefer local installs and `npx` for project tools.

## 9. Common Mistakes

- Committing `node_modules/` to git instead of `.gitignore`-ing it.
- Not committing `package-lock.json`, causing version drift.
- Putting build/test tools in `dependencies` instead of `devDependencies`.
- Running `npm install` in CI instead of `npm ci`.
- Ignoring `npm audit` security warnings.

## 10. Advanced Notes

- `npx` runs package binaries without a global install (`npx create-react-app`).
- **Workspaces** (`"workspaces"` in `package.json`) manage monorepos with multiple packages.
- `npm audit` / `npm audit fix` find and patch known vulnerabilities.
- Lifecycle scripts (`preinstall`, `postinstall`) run automatically — be cautious, they're a supply-chain risk.
- Alternatives like **pnpm** (disk-efficient, strict) and **Yarn** offer faster installs and stricter dependency resolution.
- Use `.npmrc` to configure registries, auth tokens, and install behavior.
