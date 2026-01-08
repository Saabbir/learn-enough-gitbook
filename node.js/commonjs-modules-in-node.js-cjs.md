---
description: The Original Node.js Module System
---

# CommonJS Modules in Node.js (CJS)

## **Introduction: The Early Days of Node.js**

When Node.js was first released in 2009, JavaScript never had a built-in module system.\
Ryan Dahl had to create one — and that system is **CommonJS (CJS)**.

For many years, _every Node.js project used CommonJS by default_, and even today, most npm packages still rely on it.

This article walks you through the full story of CommonJS — how it works, why it works, and how you use it confidently every day.



## **1. What Exactly Is CommonJS?**

CommonJS is a **module system designed specifically for server-side JavaScript**.

It introduces:

* `require()` → import things
* `module.exports` → export things
* A module wrapper function
* A module caching system
* A synchronous loading model

CommonJS became the foundation of Node.js.



## **2. The Module Wrapper (The Secret Behind the Scenes)**

Every CommonJS module (every `.js` file) is secretly wrapped by Node.js:

```js
(function (exports, require, module, __filename, __dirname) {
  // your code here
});
```

> 📘 **Why wrap everything?**\
> Because this gives each file:
>
> * its own private scope
> * the ability to export values
> * access to `require`, `module`, `exports`, `__filename`, and `__dirname`

#### ✔ Each file becomes its own isolated world.



## **3. Exporting in CommonJS**

There are two ways to export values:

### **Option A — Export a Whole Object with module.exports**

```js
// math.js
module.exports = {
  add(a, b) {
    return a + b;
  },
  subtract(a, b) {
    return a - b;
  }
};
```

This is the most reliable way.

### **Option B — Add Properties to exports**

```js
// math.js
exports.add = (a, b) => a + b;
exports.subtract = (a, b) => a - b;
```

> 📘 Both `exports` and `module.exports` point to the same object — until you reassign them.

### ❌ Incorrect: Reassigning exports

```js
exports = { add };
```

This **does NOT export anything** because `exports` no longer points to `module.exports`.

#### ✔ Best Practice (Important!)

> **Always use `module.exports` when exporting a complete object or function.**\
> Use `exports.foo = …` only when adding properties.



## **4. Importing in CommonJS (require)**

To import a module:

```js
const math = require('./math');
```

Or import specific functions:

```js
const { add } = require('./math');
```



## **5. How require() Works (Behind the Scenes)**

When you call:

```js
require('express')
```

Node.js checks:

1️⃣ Core built-in modules (fs, path, http…)\
2️⃣ Local file modules (`./utils.js`, `../math.js`)\
3️⃣ `node_modules` folders (walking up directories)

> 📘 After a module loads once, **Node caches it**, so repeated `require()` calls are instant.



## **6. Module Resolution Rules**

### **Local Modules**

Must start with `./` or `../`

Node looks for:

* `./file.js`
* `./file.json`
* `./file.node`
* `./file/index.js`<br>

### **Built-in Modules**

Import directly with no path:

```js
const fs = require('fs');
```

### Third-Party Modules

Stored inside `node_modules`

```js
const express = require('express');
```



## **7. Best Practices for CommonJS**

* Prefer `module.exports` over `exports`
* Do not mix CJS and ESM in the same file
* Keep modules small and focused
* Use clear file names (`math.js`, `logger.js`)
* Avoid circular dependencies



## **8. When Should You Use CommonJS?**

Use CJS when:

* Working in older Node.js environments
* Maintaining older codebases
* Working with npm packages that only support CJS



## **Summary**

CommonJS is the original Node.js module system — simple, powerful, and widely supported.

You now understand:

* How the wrapper function works
* How to export and import modules
* How require() resolves files
* The difference between exports and module.exports
* Best practices

You're ready for real-world Node.js development.
