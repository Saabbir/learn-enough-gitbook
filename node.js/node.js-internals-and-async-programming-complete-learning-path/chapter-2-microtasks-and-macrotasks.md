---
description: >-
  How JavaScript decides what runs next — the secret engine behind async
  behavior
---

# 📘 CHAPTER 2 — Microtasks & Macrotasks

## 1. Introduction

In the previous chapter, you learned what synchronous and asynchronous code is. Now we go one level deeper into something equally important:

**How does JavaScript decide the order of asynchronous operations?**

This is where many beginners get confused, because the output of async code is not always intuitive. For example:

```js
setTimeout(() => console.log("timeout"), 0);
Promise.resolve().then(() => console.log("promise"));
console.log("sync");
```

Output:

```
sync
promise
timeout
```

Why does the promise run before the timeout even though both are asynchronous?

Because of **microtasks** and **macrotasks**.

Once you understand these two queues, async code will never confuse you again.

Let’s walk through it as if you’re watching the JavaScript engine from the inside.



## 2. JavaScript’s Two Task Queues

Think of JavaScript as having two waiting rooms:

**Microtask Queue**\
**Macrotask Queue**

When JavaScript finishes running the current synchronous code, it checks:

1. Are there microtasks waiting?
2. If yes → run all microtasks
3. Only after all microtasks are done → run one macrotask
4. Then repeat

This means microtasks have **higher priority**.

This priority rule is the root of almost every async order question in JavaScript and Node.js.



## 3. What is a Microtask?

Microtasks are tiny jobs that JavaScript must run **as soon as possible**, but not inside the current synchronous execution.

Where do microtasks come from?

* Promise callbacks (`.then`, `.catch`, `.finally`)
* `queueMicrotask()`
* `MutationObserver` (browser)
* `process.nextTick()` (Node.js — even higher priority than microtasks)

Microtasks are for essential follow-up work.

Example:

```js
Promise.resolve().then(() => console.log("microtask"));
console.log("sync");
```

Output:

```
sync
microtask
```

Because JS runs microtasks right after synchronous code finishes.

Key rule:

> After every synchronous block, JS runs all pending microtasks before touching macrotasks.

This explains most async-related surprises.



## 4. What is a Macrotask?

Macrotasks are “big jobs” that are scheduled to run later.

Macrotasks come from:

* setTimeout
* setInterval
* setImmediate (Node.js)
* I/O events
* UI rendering events (browser)
* MessageChannel callbacks (browser)
* HTTP requests (Node.js)
* File system events (Node.js)
* Network events

Each macrotask runs one at a time, in the order they were queued.

Example:

```js
setTimeout(() => console.log("task"), 0);
console.log("sync");
```

Output:

```
sync
task
```

Macrotasks run only after:

* synchronous code
* all microtasks

are done.



## 5. Running Order: The Absolute Rule

JavaScript always follows this order:

1. Run all synchronous code
2. Run **all microtasks**
3. Run **one** macrotask
4. Run all microtasks again
5. Run one macrotask
6. Repeat forever

This cycle is what we call the **event loop**.

Even though we haven’t fully explored the event loop yet, you now understand the most important part of scheduling.



## 6. The Famous Example (Explained Step-by-Step)

Consider:

```js
setTimeout(() => console.log("timeout"), 0);
Promise.resolve().then(() => console.log("promise"));
console.log("sync");
```

Let’s go through the timeline.

Step 1: Synchronous code runs first

```
sync
```

Step 2: Promise callback goes to microtask queue\
Step 3: setTimeout callback goes to macrotask queue\
Step 4: Synchronous code is done → run all microtasks

```
promise
```

Step 5: No more microtasks → run one macrotask

```
timeout
```

Final output:

```
sync
promise
timeout
```

This is the exact mechanism causing behavior beginners often find confusing.



## 7. Visual Mental Model (Very Beginner-Friendly)

Imagine a work day for JS:

**Morning** → do all synchronous code\
**Lunch Break** → handle microtasks\
**Afternoon** → do 1 macrotask\
**Evening** → handle microtasks again\
Then repeat the next “day”

Microtasks always interrupt macrotasks.



## 8. queueMicrotask() — Always Fast

This function forces something to run in the microtask queue:

```js
console.log("A");
queueMicrotask(() => console.log("B"));
console.log("C");
```

Output:

```
A
C
B
```

This is useful when you want to delay something until after synchronous code but still prefer microtask priority.



## 9. process.nextTick() — Node.js Super-Microtask

Node.js has something _even faster_ than microtasks:

```js
process.nextTick(() => {});
```

nextTick callbacks run:

* **before** microtasks
* **before** macrotasks
* but **after** the current synchronous execution block

They are extremely high priority.

Example:

```js
setTimeout(() => console.log("timeout"), 0);
Promise.resolve().then(() => console.log("promise"));
process.nextTick(() => console.log("nextTick"));
console.log("sync");
```

Output:

```
sync
nextTick
promise
timeout
```

Order explained:

1. sync code
2. nextTick
3. microtasks (promise)
4. macrotask (timeout)

Because nextTick jobs run ahead of microtasks.

Important warning:

> If you schedule too many nextTicks, you can block macrotasks forever.

Example of bad behavior:

```js
function loop() {
  process.nextTick(loop);
}
loop();
```

This will freeze your program by starving I/O.



## 10. setTimeout vs setImmediate (Node.js Only)

These two confuse beginners.

```js
setTimeout(() => {}, 0);
setImmediate(() => {});
```

setImmediate → macrotask in “check” phase\
setTimeout → macrotask in “timers” phase

In real code:

* setImmediate usually runs **before** setTimeout
* but not guaranteed
* depends on phases of the event loop
* we will explain exact rules in the Event Loop chapter

For now, remember:

* Microtasks always win
* nextTick always wins over microtasks
* setImmediate and setTimeout compete in different phases



## 11. Real-World Example: Why Promises Fix Callback Timing

Two callbacks:

```js
setTimeout(() => console.log("A"), 0);
setTimeout(() => console.log("B"), 0);
console.log("C");
```

Output:

```
C
A
B
```

But with Promises:

```js
Promise.resolve().then(() => console.log("A"));
Promise.resolve().then(() => console.log("B"));
console.log("C");
```

Output:

```
C
A
B
```

Why same order?

Because microtasks preserve **queue order**, and both are microtasks.

Let’s mix them:

```js
setTimeout(() => console.log("A"), 0);
Promise.resolve().then(() => console.log("B"));
console.log("C");
```

Output:

```
C
B
A
```

This is the golden rule of async JavaScript.



## 12. A Simple Quiz (with Answers)

Q1: What runs first?

```js
Promise.resolve().then(() => console.log(1));
setTimeout(() => console.log(2), 0);
console.log(3);
```

Correct answer:

```
3
1
2
```

Q2: What runs first?

```js
process.nextTick(() => console.log("tick"));
Promise.resolve().then(() => console.log("promise"));
console.log("sync");
```

Answer:

```
sync
tick
promise
```

Q3: What runs first?

```js
setImmediate(() => console.log("I"));
setTimeout(() => console.log("T"), 0);
Promise.resolve().then(() => console.log("P"));
```

Answer:

```
P
I / T (not guaranteed, depends on event loop timing)
```

We explain this deeper in later chapters.



## 13. Summary

You now understand:

* JavaScript uses two queues: microtask and macrotask
* Microtasks always run before macrotasks
* Promise callbacks are microtasks
* setTimeout callbacks are macrotasks
* JavaScript runs:
  * sync code
  * then all microtasks
  * then one macrotask
  * then microtasks again
* Node.js adds:
  * process.nextTick (super-microtask)
  * setImmediate (special macrotask phase)
* Understanding these queues explains almost all async ordering

This knowledge is essential.\
In the next chapter, we’ll connect these ideas to the actual Node.js **event loop**, how libuv works, and how scheduling maps onto Node’s internal structure.
