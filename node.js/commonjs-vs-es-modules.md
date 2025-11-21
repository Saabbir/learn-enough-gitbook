---
description: A Clear, Complete, Beginner-Friendly Comparison
---

# CommonJS vs ES Modules

### **Introduction**

Node.js now supports **two module systems**, and beginners often ask:

> “Which one should I use?”\
> “What’s the difference?”\
> “Why are there two anyway?”

Let’s answer all of that in the simplest way possible.



## **1. The Origin Story**

#### **CommonJS (2009)**

Created for Node.js before JavaScript had a module system.

#### **ES Modules (2015+)**

Created by ECMAScript as the official JavaScript module standard.



## **2. Side-by-Side Comparison**

### CommonJS **Syntax**

```js
const fs = require('fs');
module.exports = { add };
```

### ESM **Syntax**

```js
import fs from 'fs';
export function add() {}
```

### **Loading Model**

| Feature            | CommonJS    | ES Modules   |
| ------------------ | ----------- | ------------ |
| Loading            | Synchronous | Asynchronous |
| Wrapper Function   | Yes         | No           |
| Browser Compatible | No          | Yes          |
| Static Analysis    | No          | Yes          |

### **Exports Behavior**

| Feature             | CJS (module.exports) | ESM (export)            |
| ------------------- | -------------------- | ----------------------- |
| Default export      | Yes                  | Yes                     |
| Named exports       | Yes                  | Yes                     |
| Mixed export styles | Yes                  | No                      |
| Reassign allowed    | Yes                  | No (read-only bindings) |



## **3. Module Resolution Differences**

#### **CommonJS:**

Extension optional (`require('./math')`)

#### **ESM:**

Extension required (`import './math.js'`)



## **4. Interoperability Rules**

### CJS → ESM (Easy)

Import CJS from ESM:

```js
import express from 'express';
```

Works automatically.

### ESM → CJS (Harder)

You cannot use `require()`:

```js
// ❌ doesn't work
require('./math.js');
```

Must use:

```js
const math = await import('./math.js');
```



## **5. Performance Considerations**

| Aspect       | CommonJS      | ESM                         |
| ------------ | ------------- | --------------------------- |
| Load speed   | Fast (cached) | Slightly slower (async)     |
| Optimization | Limited       | Highly optimized by engines |
| Tree-shaking | ❌             | ✔                           |

Tree-shaking = removing unused code.



## **6. When to Use What**

### **Use CommonJS if:**

* Maintaining older projects
* Using older npm packages
* Writing simple Node scripts

### **Use ES Modules if:**

* Starting modern projects
* Want browser/server sharing
* Using TypeScript/Vite/Next.js
* Writing future-proof code



## **7. Migration Strategy (CJS → ESM)**

1. Add `"type": "module"` to package.json
2. Update imports (`require` → `import`)
3. Add `.js` extensions
4. Replace `module.exports` with `export default` / `export`
5. Update tools (Jest, ESLint, etc.)



## **Summary**

CommonJS is the past (and still widely used).\
ES Modules are the present and future.

Today you learned:

* Syntax and conceptual differences
* How Node loads CJS vs ESM
* How to migrate safely
* When to use which module system
* How they interoperate

With this knowledge, you can work confidently in any Node.js codebase.
