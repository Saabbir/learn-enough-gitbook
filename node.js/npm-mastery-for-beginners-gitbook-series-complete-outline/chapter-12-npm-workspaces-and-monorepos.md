---
description: >-
  A comprehensive, practical guide to using npm Workspaces to manage
  multi-package projects.
---

# 📘 CHAPTER 12 — npm Workspaces & Monorepos

## 1. Introduction

As projects grow, you eventually need to manage:

* multiple related packages
* shared utilities
* backend + frontend in the same repo
* API services + web app + admin UI
* library + demo app + documentation site

Managing these in **separate repositories** becomes difficult over time:

* duplicated configuration
* duplicated dependencies
* inconsistent versions
* difficulty sharing code
* lots of boilerplate
* more complicated CI/CD

This is where **monorepos** and **npm workspaces** come in.

Modern JavaScript ecosystems (React, Next.js, Expo, Astro, Shopify, Google, Meta) use monorepos heavily.

npm now supports monorepos **natively**, without needing extra tools.

This chapter teaches you:

* what a monorepo is
* how npm workspaces work
* how to structure workspace directories
* how to share dependencies
* how to link packages together
* how to run scripts across workspaces
* best practices for real-world projects

By the end, you'll be able to build scalable multi-package projects professionally.

## 2. What Is a Monorepo?

A **monorepo** (monolithic repository) is a single Git repository containing **multiple related projects**.

Example layout:

```
my-project/
  packages/
    api/
    web/
    utils/
```

Instead of splitting them into separate repositories, everything lives together.

### Benefits of a monorepo:

* Shared tooling
* Shared configs (ESLint, Prettier, TypeScript, Vite, Babel)
* Shared dependencies
* Easier refactoring across projects
* Better developer experience
* Easier CI/CD
* Versioned together
* Atomic commits (change everything in one commit)

Popular monorepo tools:

* npm workspaces
* pnpm workspaces
* Yarn workspaces
* TurboRepo
* Nx
* Lerna

npm workspaces are built-in, lightweight, and ideal for learning.

## 3. What Are npm Workspaces?

Workspaces allow npm to manage multiple packages **inside one repository**.

Features include:

* a single node\_modules at the root
* shared dependencies
* linking local packages automatically
* running scripts across packages
* installing dependencies per workspace
* consistent versioning

Workspaces are defined in the root `package.json`.

Example:

```json
{
  "name": "my-monorepo",
  "private": true,
  "workspaces": ["packages/*"]
}
```

npm will treat everything inside `packages/` as separate packages.

## 4. The Standard Workspace Directory Structure

Typical structure:

```
my-monorepo/
  package.json
  node_modules/
  packages/
    api/
      package.json
    web/
      package.json
    shared/
      package.json
```

Root package.json:

```json
{
  "name": "my-monorepo",
  "private": true,
  "workspaces": [
    "packages/*"
  ]
}
```

Each workspace must have its own package.json.

Example for packages/api:

```json
{
  "name": "@company/api",
  "version": "1.0.0"
}
```

## 5. Why Root Must Have `"private": true`

This prevents accidentally publishing the monorepo root to npm.

Always include:

```json
"private": true
```

Every monorepo requires this.

## 6. Installing Dependencies in a Workspace

You can install dependencies:

### Install into a specific workspace

```
npm install express --workspace packages/api
```

Or shorter:

```
npm install express -w api
```

### Install at the root (shared dependencies)

```
npm install typescript -D
```

npm automatically hoists common dependencies to the root node\_modules.

This reduces duplication and improves install speed.

## 7. Local Package Linking (The Magic of Workspaces)

Suppose you have:

```
packages/
  utils/
  api/
```

And your api package depends on utils:

packages/api/package.json:

```json
"dependencies": {
  "@company/utils": "1.0.0"
}
```

As long as the directory name matches, npm will automatically **symlink** it instead of downloading from npm.

This means:

* real-time updates
* instant linking
* no need to publish utilities
* shared code across packages

This is one of the biggest advantages of monorepos.

## 8. Running Scripts Across Workspaces

If each package has scripts, like:

packages/api/package.json:

```json
"scripts": {
  "dev": "nodemon index.js"
}
```

packages/web/package.json:

```json
"scripts": {
  "dev": "vite"
}
```

You can run:

```
npm run dev -w api
npm run dev -w web
```

Or run the same script across all workspaces:

```
npm run build --workspaces
```

## 9. Running Commands at the Root Level

From the root, you can also install dependencies for all workspaces:

```
npm install
```

This:

* installs shared dependencies
* installs workspace-specific dependencies
* links local packages

npm handles everything automatically.

## 10. Detailing Dependency Hoisting (Important!)

npm hoists shared dependencies to the root:

```
node_modules/
  express/
  axios/
```

But workspace-specific dependencies stay inside:

```
packages/api/node_modules/
```

npm decides based on:

* version compatibility
* dependency conflicts
* peer dependency requirements

This reduces duplication and speeds up installations.

## 11. Example: A Real Monorepo with Shared Code

Example structure:

```
my-app/
  package.json
  packages/
    web/
    api/
    shared/
```

shared contains:

```
export function formatCurrency(value) {
  return `$${value.toFixed(2)}`
}
```

web and api can import it using:

```js
import { formatCurrency } from "@company/shared"
```

Changes update **instantly** thanks to symlinks.

## 12. Versioning Strategies

There are two monorepo versioning strategies:

### 1. Unified Versioning (recommended for apps)

All packages share the same version.

Example:

```
1.2.0
```

Used by:

* React
* Next.js
* Angular CLI

### 2. Independent Versioning (recommended for libraries)

Each package has its own version.

Used by multi-package library ecosystems.

## 13. Publishing Workspace Packages to npm

If you want to publish:

```
npm publish -w packages/shared
```

npm publishes only that package.

You can also publish multiple packages:

```
npm publish --workspaces
```

npm respects version fields inside each package.json.

## 14. Workspaces vs Lerna, Turborepo, pnpm

### npm workspaces

Built-in, simple, great for beginners.

### Yarn or pnpm workspaces

More features, better performance.

### Lerna

Manages versioning and publishing workflows.

### Turborepo / Nx

Focus on task running, caching, and performance.

Professional monorepos often combine:

* pnpm workspaces
* Turborepo
* CI/CD pipelines
* package publishing tools

npm workspaces are the perfect entry point.

## 15. Advanced: Filtering, Running Only Certain Workspaces

To run scripts only on some workspaces:

```
npm run build --workspace web
npm run test --workspace api
```

To run for multiple:

```
npm run lint --workspaces --include-workspace-root
```

To ignore certain workspaces:

```
npm install --workspaces --no-workspaces-update
```

## 16. Common Mistakes to Avoid

### Bad: Not using “private: true”

Could accidentally publish your whole repo.

### Bad: Using mismatched dependency names

Workspace linking won’t work.

### Bad: Deeply nested workspace folders

Use a clean structure:

```
packages/project-name
```

### Bad: Mixing dedicated repos with monorepos carelessly

Choose monorepo intentionally, not by accident.

### Bad: Letting dependencies drift

Use shared dependencies whenever possible.

### Bad: Mixing multiple package managers

Pick npm, Yarn, or pnpm per repo — not all of them.

## 17. Best Practices

* Keep workspace folder names short and clear
* Use shared utilities to avoid duplication
* Put TypeScript, ESLint, Prettier configs at the root
* Share node\_modules at the root for speed
* Use local linking instead of publishing packages
* Separate apps and libraries clearly
* Add root-level scripts for convenience
* Use commit hooks for consistency

Monorepos work best when consistent and organized.

## 18. Summary

In this chapter, you learned:

* what monorepos are and why they are useful
* how npm workspaces work
* how to structure workspace directories
* how to install dependencies per workspace
* how npm hoists shared dependencies
* how to link local packages using symlinks
* how to run scripts for individual or all workspaces
* how workspace versioning works
* how to publish workspace packages
* how npm workspaces compare to other tools
* best practices and common mistakes

npm workspaces provide a simple yet powerful foundation for building scalable multi-package projects — without needing extra tooling.
