---
description: Modern JavaScript Modules for the Modern Web
---

# ES Modules in Node.js (ESM)

### **Introduction: The Future Arrives**

JavaScript finally got an official module system: **ES Modules (ESM)**.

The browser adopted it.\
Node.js adopted it.\
And now ESM is the **modern standard** for JavaScript projects.

Let’s explore ESM from the ground up.



## **1. What Are ES Modules?**

ES Modules are the official JavaScript module system, defined by ECMAScript.

They use:

* `import`
* `export`

And bring powerful new behavior:

* Static analysis (faster bundling)
* Async loading
* Browser + server compatibility
* Better tooling support



## **2. How to Enable ES Modules in Node.js**

Node supports ESM in two ways.

### Option 1 — Use .mjs Extension

```bash
index.mjs
math.mjs
```

Node treats all `.mjs` files as ES modules.

### Option 2 — package.json type: module

```json
{
  "type": "module"
}
```

Now **all `.js` files become ESM**.



## **3. Exporting in ES Modules**

### **Named Exports**

```js
export function add(a, b) {}
export const PI = 3.14;
```

### **Export List**

```js
function add() {}
function subtract() {}

export { add, subtract };
```

### **Default Export**

```js
export default function () {}
```

You can only have **one default export per file**.



## **4. Importing in ES Modules**

### **Import specific values**

```js
import { add } from './math.js';
```

### **Import everything**

```js
import * as math from './math.js';
```

### **Import default**

```js
import add from './math.js';
```

### **Dynamic Import (Async)**

```js
const math = await import('./math.js');
```

> 💡 This is the ESM equivalent of `require()`.



## **5. ESM Module Resolution Differences**

Node.js ESM is strict.

#### ✔ You MUST include file extensions

```js
import './math.js'; // correct
import './math';    // ❌ error
```

#### ✔ Directory imports require explicit entry file

```js
import './utils/index.js';
```



## **6. Interoperability (CJS ↔ ESM)**

Node.js allows mixing — but with rules.

### Importing CJS from ESM

Works automatically:

```js
import express from 'express';
```

Default import receives `module.exports`.

### Importing ESM from CJS

You must use dynamic import:

```js
const math = await import('./math.js');
```

Synchronous `require()` **cannot** load ESM modules.



## **7. ES Modules Are the Future**

Use ESM when:

* Starting new projects
* Targeting modern Node.js
* Writing libraries for both browser + Node
* Building apps with bundlers (Vite, Webpack, SWC)



## **Summary**

ES Modules give JavaScript a modern, native module system.

You learned:

* How to enable ESM
* How import/export works
* Static vs dynamic imports
* File extension rules
* How CJS + ESM interoperate

You’re now ready to confidently build modern Node.js projects.
