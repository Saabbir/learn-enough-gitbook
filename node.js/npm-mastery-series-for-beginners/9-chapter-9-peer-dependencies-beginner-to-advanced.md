---
description: >-
  A complete, practical understanding of peer dependencies and how they really
  work in npm.
---

# 9️⃣ CHAPTER 9 — Peer Dependencies (Beginner to Advanced)

## 1. Introduction

If you've used npm long enough, you’ve seen an intimidating warning like:

```
npm WARN requires a peer of react@^18 but none is installed
```

or

```
npm WARN ERESOLVE overriding peer dependency
```

Or worse — your app broke due to a version mismatch between React, ESLint plugins, or UI libraries.

This chapter explains **exactly what peer dependencies are**, why they exist, how they work, and how to fix all peer dependency warnings and errors correctly.

By the end, you will understand peer dependencies with the same confidence as an experienced package maintainer.

## 2. What Are Peer Dependencies?

A **peer dependency** is a dependency that your package **expects the consuming project to provide**.

In simpler words:

> A peer dependency says:\
> “I need your project to already have this installed, and it must be a compatible version.”

Example from a React plugin:

```json
"peerDependencies": {
  "react": "^18.0.0"
}
```

This means:

* The plugin _does not install React_
* It _expects you_ to install React
* It requires _React v18_ or higher, but not v19

Peer dependencies are not automatically installed by npm.

## 3. Why Do Peer Dependencies Exist?

Peer dependencies exist because some libraries must use the **same instance** as the consuming project.

Example:

* A React plugin must use **the same React instance** as the main app
* An ESLint plugin must use **the ESLint installed in the project**
* A styling plugin must use **the same version of PostCSS**

If a plugin installs its own instance, things break.

### Real-world example: React

If a plugin installs its own React version:

```
node_modules/
   react (your version)
   plugin/
      node_modules/
         react (its own version)
```

React hooks break, contexts break, components break.

The only solution: _both must share the SAME React instance._

Peer dependencies enforce this.

## 4. Where Peer Dependencies Are Used

You will find peer dependencies in:

* React libraries (react-router, react-hook-form)
* ESLint plugins
* Webpack loaders
* PostCSS plugins
* Prettier plugins
* UI component libraries
* Framework extensions

These packages must integrate with a “parent” library.

## 5. How npm Treats Peer Dependencies Today

Before npm v7:

* Peer dependencies were NOT auto-installed
* npm simply printed warnings

After npm v7:

* Peer dependencies are AUTO-INSTALLED whenever possible
* But if incompatible versions exist, npm prints conflicts

This is why you see errors like:

```
ERESOLVE could not resolve dependency tree
```

npm tries to install peer dependencies automatically, but fails when versions conflict.

## 6. Example of Peer Dependencies

Let's say you install a React plugin:

```
npm install my-react-plugin
```

The plugin’s package.json:

```json
"peerDependencies": {
  "react": "^18.0.0",
  "react-dom": "^18.0.0"
}
```

If your project already has:

```
react 17.0.0
```

npm will complain:

```
npm ERR! could not resolve peer dependency: react@^18.0.0
```

Solution:

```
npm install react@^18 react-dom@^18
```

## 7. Why Peer Dependencies Are Not Installed Automatically

If npm installs peer dependencies automatically, it could:

* install mismatched versions
* override user versions
* break applications
* install multiple versions of React or ESLint
* create unresolvable dependency trees
* cause plugins to break silently

This is why the **user must explicitly install them**.

Peer dependencies are similar to:

> “My library works only if you already have this package installed in your project.”

## 8. Peer Dependencies vs Dependencies

Let’s compare them:

### dependencies

Installed automatically

```
npm install express
```

Express will be installed into node\_modules.

### devDependencies

Installed only during development.

### peerDependencies

Not auto-installed\
Must be installed by the user\
Used for compatibility relationships

Example:

A React plugin doesn’t want its own react installed.\
It wants **your** version.

## 9. Real-world Example: ESLint Plugins

ESLint (the main linter) is installed as:

```
npm install eslint -D
```

ESLint plugins require:

```json
"peerDependencies": {
  "eslint": "^9.0.0"
}
```

Meaning:

* They will break if you have ESLint 7
* They will break if you have ESLint 8
* They want version 9

If versions mismatch, npm warns.

## 10. Real-world Example: Webpack Loaders

Webpack loaders often require Webpack itself:

```json
"peerDependencies": {
  "webpack": "^5.0.0"
}
```

If you incorrectly have Webpack 4 installed:

```
npm ERR! peer webpack@"^5.0.0" from css-loader@6.0.0
```

Your build will fail.

## 11. How To Fix Peer Dependency Warnings (Step-by-Step)

When you see:

```
requires a peer of react@^18 but none is installed
```

Here’s how to fix it:

### Step 1: Identify required versions

Check the plugin’s peerDependencies.

### Step 2: Install the required version explicitly

```
npm install react@18 react-dom@18
```

### Step 3: Reinstall the plugin

Optional but sometimes required.

```
npm install my-react-plugin
```

### Step 4: Ensure no duplicates

```
npm ls react
```

If you see multiple versions, fix conflicts.

## 12. Peer Dependency Conflicts

A conflict happens when two packages require incompatible versions.

Example:

* A requires react@18
* B requires react@17

You cannot satisfy both.

This results in:

```
npm ERR! ERESOLVE unable to resolve dependency tree
```

Solutions:

1. Upgrade B to support React 18
2. Downgrade A
3. Choose a different library
4. Use overrides (in advanced cases)

## 13. Optional Peer Dependencies

Some packages mark peer deps as optional:

```json
"peerDependenciesMeta": {
  "typescript": {
    "optional": true
  }
}
```

This means:

* your project can use the library with or without TypeScript
* no error will be thrown if missing

Optional peer dependencies are useful for plugins that support multiple environments.

## 14. The Most Common Peer Dependency Mistakes

Here are mistakes beginners make:

* Ignoring peer dependency warnings
* Installing packages without checking compatibility
* Mixing incompatible versions of React, ESLint, or Webpack
* Assuming npm will fix peer deps automatically
* Using `--force` or `--legacy-peer-deps` incorrectly

Using `--force` hides the problem but creates unstable dependency trees.

## 15. When to Use `--legacy-peer-deps`

```
npm install --legacy-peer-deps
```

This tells npm to ignore peer dependency rules and behave like npm v6.

Use only when:

* installing old packages
* using outdated libraries
* maintaining legacy apps

Never use it for modern projects.\
It can silently break functionality.

## 16. When to Use `--force`

Almost never.

```
npm install --force
```

Forces npm to install conflicting versions.

This can:

* install wrong versions
* break the app
* make builds unstable

Avoid unless you're debugging dependency issues.

## 17. Tools to Help Resolve Peer Dependencies

### npx npm-check-updates

Shows which major versions support your environment.

### npm ls

Shows nested dependency versions and conflicts.

### npm outdated

Shows version compatibility.

### GitHub Releases / Changelogs

Always read release notes before upgrading React, ESLint, Webpack, etc.

## 18. Best Practices for Working with Peer Dependencies

* Always read the plugin’s peerDependencies section
* Never ignore warnings
* Upgrade major frameworks first (React, Webpack, ESLint)
* Choose plugins that support your version
* Avoid forcing installs
* Test compatibility before upgrading major versions

## 19. Summary

In this chapter, you learned:

* what peer dependencies actually are
* why npm does not install them automatically
* how npm handles them in modern versions
* how peer dependencies prevent multiple React instances
* how they enforce compatibility for ESLint, Webpack, and PostCSS
* how to fix peer dependency conflicts
* when to use optional peer dependencies
* when NOT to use `--force` or `--legacy-peer-deps`
* best practices followed by professional developers

Peer dependencies are one of the most confusing parts of npm, but once mastered, they help you build compatible, stable, and predictable applications.
