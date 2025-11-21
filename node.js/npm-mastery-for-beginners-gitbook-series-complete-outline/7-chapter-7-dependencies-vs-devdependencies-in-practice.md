---
description: >-
  A practical, in-depth guide to understanding and using dependency types
  correctly.
---

# 7️⃣ CHAPTER 7 — dependencies vs devDependencies in Practice

## 1. Introduction

Every Node.js project contains two key sections in `package.json`:

```json
"dependencies": {}
"devDependencies": {}
```

At first, these may seem similar. Both contain packages. Both get installed. Both appear in node\_modules.

But in real-world projects:

* they behave differently in development
* they behave differently in production
* cloud platforms treat them differently
* build tools treat them differently
* npm installs them differently
* mistakes here can break your deployment

This chapter goes beyond definitions and teaches **real, practical differences** that professional developers must understand.

## 2. The Simple Definition

Let’s start with the basics.

### dependencies

Packages your application **needs at runtime**, when it is actually running in production.

Examples:

* Express (your server can't run without it)
* Mongoose / Prisma
* Axios
* React (for client-side apps)
* JSON Web Token libraries

### devDependencies

Packages used **only while developing**, not when the app is running.

Examples:

* TypeScript
* Webpack
* Babel
* ESLint / Prettier
* Testing tools (Jest, Mocha)
* Nodemon

If the package is only needed while writing code, it belongs in `devDependencies`.

This is the conceptual difference.\
Now let’s go deeper.

## 3. How npm Treats Them During Installation

### Installing production dependencies only:

```
npm install --production
```

This installs:

* dependencies ✔
* devDependencies ✖

Useful for:

* Docker images
* servers
* CI environments
* Lambdas

### Installing everything (default):

```
npm install
```

This installs:

* dependencies ✔
* devDependencies ✔

Used during development.

## 4. How Deployment Platforms Treat Them

Different hosting providers behave differently.\
This is where many beginners run into issues.

### Vercel / Netlify / Railway

These platforms:

* install devDependencies during build
* but remove them for production bundles
* rely heavily on `package.json` categories

If you accidentally put Webpack or TypeScript in `dependencies`:

* your deployed app becomes bloated
* build times increase
* bigger cold starts on serverless functions

If you accidentally put Express in `devDependencies`:

Your deployment will fail:

```
Error: Cannot find module 'express'
```

### AWS Lambda / Cloudflare Workers

These environments often:

* **ignore devDependencies entirely**
* require bundling for production
* run in minimal environments

Therefore, putting runtime libraries in devDependencies causes runtime failures.

### Docker

Using:

```
npm install --production
```

ensures your Docker image:

* includes only runtime essentials
* is smaller
* is more secure
* builds faster

## 5. How Build Tools Use Them

Most JavaScript projects use bundlers or build steps:

* Vite
* Webpack
* Rollup
* Parcel

These tools:

* live in devDependencies
* bundle code into a final “production build”
* eliminate devDependencies entirely from hosting environments

Because bundlers remove devDependencies from the production output, tools like Webpack should never go in `dependencies`.

## 6. What Happens if You Misclassify Dependencies?

### Putting a runtime dependency into devDependencies

Example mistake:

```json
"devDependencies": {
  "express": "^4.19.0"
}
```

Local development works.\
Production deployment fails instantly.

Error:

```
Error: Cannot find module 'express'
```

Production never installs Express → server cannot start.

### Putting dev tools into dependencies

Example mistake:

```json
"dependencies": {
  "webpack": "^5",
  "typescript": "^5",
  "eslint": "^9"
}
```

This causes:

* bigger node\_modules
* slower deploys
* security risks
* unnecessary files in Docker images
* slower cold starts
* large Lambda bundles

In large apps, this adds MBs of unnecessary code.

## 7. Examples of dependency classifications

### Should be in dependencies:

* express
* fastify
* mongoose
* prisma
* axios
* cors
* jsonwebtoken
* bcrypt
* react (client-side apps)
* next (Next.js runtime)

### Should be in devDependencies:

* typescript
* vite
* webpack
* jest
* mocha
* eslint
* prettier
* nodemon
* tailwindcss
* babel
* ts-node

If it affects **your code while writing**, it's a devDependency.

If it affects **your app while running**, it’s a dependency.

## 8. Dependencies in Different Types of Projects

### 1. Backend Node.js app

Runtime deps:

* Express
* Prisma
* PostgreSQL client

Dev deps:

* Nodemon
* TypeScript
* ESLint

### 2. React / Vue / Svelte frontend

Runtime deps:

* React
* Redux
* Axios

Dev deps:

* Vite
* Webpack
* Babel
* ESLint

### 3. Library / npm package

Runtime deps:

* Whatever your library needs to work

Dev deps:

* Testing tools
* Build tools
* Compilers

Library authors must be especially careful.

## 9. How Dependencies Affect Bundle Size

Frontend frameworks bundle your app for browsers.

Bundlers only include:

* your runtime dependencies
* and the files you actually import

devDependencies are completely ignored during bundling.

But if you mistakenly add something heavy to dependencies (e.g., lodash-es, graph libraries), your bundled output grows unnecessarily.

## 10. How Dependencies Affect Serverless Functions

Serverless providers (Vercel, AWS Lambda, Netlify Functions):

* package dependencies inside a function bundle
* perform tree-shaking
* remove devDependencies
* analyze imports

If you misplace dependencies:

* cold starts increase
* deployment packages grow huge
* runtime errors happen

Correct organization is critical.

## 11. How npm ci Treats Dependencies vs DevDependencies

Running:

```
npm ci
```

Will:

* install dependencies
* install devDependencies
* fail if package.json and lockfile disagree

For CI builds:

* devDependencies are needed for tests and builds
* but not needed for the final production environment

That’s why teams often:

* run npm ci for building
* run npm prune --production before packaging

## 12. Special Case: Next.js, Remix, Astro

These modern frameworks have multiple phases:

* dev mode
* build mode
* runtime mode

Runtime mode may run on:

* server
* edge runtime
* client-side browser

Some dependencies are needed only during build.

Examples:

```
devDependencies: tailwindcss, postcss, typescript
dependencies: next, react, react-dom
```

Misplacing these causes:

* deployment failures
* missing modules
* oversized bundles

## 13. How Node Determines Runtime Dependencies

Node uses `require()` or `import` statements to find modules.

If your runtime code imports something:

```js
import express from "express"
```

It **must** be in dependencies.

If only your build tools import something:

```js
// Used in build only
import eslint from "eslint";
```

It **must** be in devDependencies.

## 14. Using npm install Correctly

By default:

```
npm install express
```

Adds to dependencies.

To add to devDependencies:

```
npm install -D express
```

or:

```
npm install --save-dev express
```

Always install deliberately.

## 15. Best Practices

* Only put runtime code in dependencies
* Keep devDependencies lean
* Avoid unnecessary packages
* Use npm prune --production for minimal builds
* Always verify classification before deploying
*   For Docker, use:

    ```
    npm install --production
    ```
*   For CI builds:

    ```
    npm ci
    ```

## 16. Common Mistakes to Avoid

* Putting Express or React in devDependencies
* Putting TypeScript or ESLint in dependencies
* Installing everything globally
* Using npm install \<pkg> without thinking
* Blindly copying code from StackOverflow

## 17. Summary

In this chapter, you learned:

* the real-world differences between dependencies and devDependencies
* how npm treats them differently
* how deployment platforms handle them
* how build processes rely on them
* how misclassification causes runtime errors
* how to classify them correctly
* best practices followed by professional teams

Correct dependency classification is essential for:

* working production apps
* secure deployments
* fast builds
* predictable environments
* low bundle sizes
