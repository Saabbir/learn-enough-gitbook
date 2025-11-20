---
description: >-
  A complete, professional, beginner-friendly guide to the most important file
  in every Node.js project.
---

# 2️⃣ CHAPTER 2 — Understanding package.json (Deep Dive)

## **1. Introduction**

Every modern JavaScript project—whether built with Node.js, React, Next.js, Vite, Electron, or even CLI tools—relies on one essential file:

#### **`package.json`**

It describes:

* what your project _is_
* what your project _depends on_
* how your project can be _run, built, tested_
* what versions of Node it supports
* what files should be published
* how modules should be resolved
* and much more

Understanding `package.json` is one of the most important steps in learning Node.js.\
This chapter teaches you **exactly how this file works—field by field, in detail.**

***

## **2. What Is `package.json`?**

`package.json` is a structured JSON document that defines:

* **metadata** (name, description, author)
* **dependency lists**
* **scripts** you can run
* **versioning rules**
* **entry points**
* **package configurations**
* **publishing settings**

It acts as a _manifest_ for your application or library.

Example of a minimal `package.json`:

```json
{
  "name": "my-app",
  "version": "1.0.0"
}
```

A real-world one can look like:

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "description": "A demo app",
  "main": "index.js",
  "scripts": {
    "dev": "nodemon index.js",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.19.0"
  },
  "devDependencies": {
    "nodemon": "^3.0.2"
  }
}
```

***

## **3. Mandatory Fields**

Only **two fields are truly required** to make a valid `package.json`:

#### ✔ `"name"`

#### ✔ `"version"`

Everything else is optional.

***

## **4. Field-by-Field Deep Explanation**

Below is the **complete** list of commonly used fields, explained clearly and in depth.

***

### **4.1 `"name"`**

This is the name of your project or package.\
Rules:

* must be lowercase
* cannot contain spaces
* must be URL-safe
* must be unique if published to npm

Examples:

```
"shopify-app"
"express"
"my-awesome-tool"
```

If publishing to npm, names like `"react"` or `"vue"` are obviously already taken.

***

### **4.2 `"version"`**

This follows **Semantic Versioning (SemVer)**:

```
"version": "MAJOR.MINOR.PATCH"
```

Examples:

* `"1.0.0"` → stable release
* `"0.1.0"` → early development
* `"2.1.3"` → update with new features

More details about versioning will be covered in **Chapter 3**.

***

### **4.3 `"description"`**

A short explanation of your project.

Used for:

* npm registry display
* search indexing
* clarity for other developers

***

### **4.4 `"main"` — The Entry Point (CommonJS)**

Defines what file runs when someone imports your package using:

```js
require("your-package")
```

Example:

```
"main": "index.js"
```

If omitted, Node assumes `index.js` at the project root.

***

### **4.5 `"type"` — Module System**

Controls whether Node uses:

* **CommonJS (require)**
* **ES Modules (import)**

#### `"type": "commonjs"` (default)

You can use:

```js
const express = require("express")
```

#### `"type": "module"`

You must use:

```js
import express from "express"
```

This field changes **how Node interprets your .js files**.

***

### **4.6 `"scripts"` — Automation Commands**

This is one of the most important parts of `package.json`.

You can define commands such as:

```json
"scripts": {
  "start": "node index.js",
  "dev": "nodemon index.js",
  "build": "webpack",
  "test": "jest",
  "lint": "eslint ."
}
```

Run them with:

```
npm run dev
npm run build
npm test
npm start
```

#### Special cases:

* `npm start` → runs `"start"` script **without** writing `npm run start`
* `npm test` → same shortcut for `"test"`

Scripts allow you to build powerful workflows.

***

### **4.7 `"dependencies"` — Production Libraries**

Packages your project needs when running in production.

Example:

```json
"dependencies": {
  "express": "^4.19.0",
  "axios": "^1.6.7"
}
```

When someone installs your project:

```
npm install
```

These get installed.

***

### **4.8 `"devDependencies"` — Development Tools**

Tools used **only during development**, never in production.

Example:

```json
"devDependencies": {
  "nodemon": "^3.0.2",
  "typescript": "^5.2.2",
  "eslint": "^9.0.0"
}
```

Deployment platforms (Vercel, Netlify) ignore devDependencies unless needed.

***

### **4.9 `"peerDependencies"` — Required Sibling Packages**

Required when building **plugins** or **extensions**.

Example from a React plugin:

```json
"peerDependencies": {
  "react": "^18.0.0"
}
```

This means:

> The user must install React manually.

More about this in Chapter 9.

***

### **4.10 `"peerDependenciesMeta"` (Advanced)**

Allows marking peer deps as optional:

```json
"peerDependenciesMeta": {
  "typescript": {
    "optional": true
  }
}
```

***

### **4.11 `"optionalDependencies"`**

These can fail to install, and your project will still work.

Useful for operating system-specific packages.

Example:

```json
"optionalDependencies": {
  "fsevents": "^2.3.2"
}
```

***

### **4.12 `"engines"`**

Specify what versions of Node & npm your project supports:

```json
"engines": {
  "node": ">=18",
  "npm": ">=9"
}
```

You can enforce this strictly in some environments.

***

### **4.13 `"bin"` — Creating CLI Tools**

If you write a CLI tool, you expose commands under `"bin"`:

```json
"bin": {
  "mycli": "./cli.js"
}
```

Now users can run:

```
mycli
```

***

### **4.14 `"keywords"`**

Helpful for discoverability in the npm registry.

***

### **4.15 `"license"`**

Important for open-source projects.

Common licenses:

* MIT
* ISC
* Apache-2.0
* GPL

***

### **4.16 `"author"` & `"contributors"`**

Useful for projects with multiple maintainers.

***

### **4.17 `"repository"`**

Links your code to GitHub:

```json
"repository": {
  "type": "git",
  "url": "https://github.com/saabbir/my-app.git"
}
```

***

### **4.18 `"bugs"`**

Point users to issue trackers:

```json
"bugs": {
  "url": "https://github.com/saabbir/my-app/issues"
}
```

***

### **4.19 `"homepage"`**

Shows the project website or documentation landing page.

***

### **4.20 `"files"` — What to Publish to npm**

Controls what files get uploaded when you run `npm publish`.

```json
"files": [
  "dist",
  "src",
  "README.md"
]
```

Excludes everything else by default.

***

### **4.21 `"private"` — Prevent Accidental Publication**

```
"private": true
```

This **blocks npm publish** entirely.\
Should be used for:

* private projects
* monorepos
* internal tools

***

### **4.22 `"config"` — Pass Variables to Scripts**

Example:

```json
"config": {
  "port": "4000"
}
```

Access in scripts:

```bash
npm_package_config_port
```

Or in Node:

```js
process.env.npm_package_config_port
```

Not commonly used, but powerful.

***

### **4.23 `"overrides"` (npm 8+)**

Override specific dependency versions across dependency tree:

```json
"overrides": {
  "lodash": "4.17.21"
}
```

Useful when a dependency has a vulnerability.

***

### **4.24 `"resolutions"` (Yarn only)**

Included for completeness (not used by npm).\
Similar to overrides.

***

### **4.25 `"exports"` — Modern Module Resolution**

Defines which files can be imported:

```json
"exports": {
  ".": "./src/index.js",
  "./helpers": "./src/helpers.js"
}
```

If `"exports"` is present:

* `"main"` may be ignored
* Files outside exports become inaccessible
* Better for security and stability

This is now standard in modern Node libraries.

***

## **5. Real Project Example (Explained in Detail)**

Here’s a realistic `package.json`:

```json
{
  "name": "shopify-toolkit",
  "version": "2.1.0",
  "description": "A toolkit for Shopify theme development",
  "main": "dist/index.js",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "lint": "eslint src --ext .js,.ts",
    "format": "prettier --write ."
  },
  "dependencies": {
    "axios": "^1.6.7",
    "chalk": "^5.3.0"
  },
  "devDependencies": {
    "typescript": "^5.2.2",
    "eslint": "^9.0.0",
    "vite": "^5.0.3",
    "prettier": "^3.1.0"
  },
  "engines": {
    "node": ">=18"
  },
  "files": [
    "dist"
  ],
  "repository": {
    "type": "git",
    "url": "https://github.com/saabbir/shopify-toolkit.git"
  },
  "license": "MIT"
}
```

This file:

* Defines scripts
* Sets module type
* Locks Node version
* Controls what gets published
* Separates dev and production dependencies
* Uses modern tools (TypeScript + Vite)

***

## **6. Best Practices for `package.json`**

#### ✔ Always add `"private": true"` if you're not publishing

This prevents catastrophic accidental publishes.

#### ✔ Keep scripts simple

Offload logic into build tools, not giant scripts.

#### ✔ Use semantic versioning properly

You will learn this in Chapter 3.

#### ✔ Keep dependencies clean

Remove unused packages regularly.

#### ✔ Never manually edit node\_modules

Everything comes from package.json.

#### ✔ Keep `package.json` human-friendly

Use meaningful scripts like:

```
"lint": "eslint ."
"format": "prettier --write ."
"dev": "vite"
```

***

## **7. Common Beginner Mistakes**

#### ❌ Using spaces in `"name"`

#### ❌ Putting dev tools in `"dependencies"`

#### ❌ Forgetting `"type": "module"` when using `import`

#### ❌ Publishing confidential code accidentally

#### ❌ Using `"main"` incorrectly in ES module projects

#### ❌ Editing package-lock.json manually

#### ❌ Adding too many scripts instead of using script chaining tools

***

## **8. Summary**

In this chapter, you learned:

* What `package.json` is and why it exists
* The purpose of each field
* How scripts work
* Differences between dependencies & devDependencies
* Advanced fields like exports, overrides, engines
* Best practices
* Common mistakes
* How real-world projects structure package.json

You now understand **one of the most crucial files in the entire Node.js ecosystem**.
