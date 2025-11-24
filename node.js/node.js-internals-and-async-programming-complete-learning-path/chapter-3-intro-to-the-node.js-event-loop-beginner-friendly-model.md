---
description: >-
  Understanding how Node.js “waits” without blocking, and how it magically
  handles thousands of tasks on a single thread.
---

# 📘 CHAPTER 3 — Intro to the Node.js Event Loop (Beginner-Friendly Model)

## 1. Introduction

The event loop is the beating heart of Node.js. It is the reason Node can handle thousands of requests at the same time on just one thread. It is also one of the most misunderstood topics for beginners, because from the outside, it looks like Node.js is doing many things in parallel… but it isn’t.

JavaScript itself only runs on **one thread**. One person doing one task at a time. But Node.js surrounds JavaScript with an environment that can perform background work, wait for things, listen for I/O events, and notify JavaScript when something is ready.

The event loop is the mechanism that decides _when_ JavaScript should run your code, _when_ it should pause, and _when_ it should pick up results from background tasks.

Understanding this makes you a true Node.js developer.

Let’s build the mental model step by step.

## 2. JavaScript Runs on One Thread

JavaScript itself cannot do two things at once. It has:

* one call stack
* one thread of execution
* one line of code running at any given moment

If you block this thread (for example with a big loop or a slow synchronous operation), everything stops.

But servers need concurrency. They need to handle multiple requests without waiting.

Node.js solves this by pairing JavaScript with an engine underneath it called **libuv**.

## 3. Node.js Has Two Worlds

There are two “worlds” working together:

**World 1: JavaScript**\
Runs your code line by line, on a single thread.

**World 2: libuv (C/C++ layer)**\
Handles slow operations like:

* reading files
* writing files
* network events
* DNS lookups
* timers
* socket communication

JavaScript delegates tasks to libuv, continues running other code, and later receives a callback from libuv.

You can think of libuv as a powerful assistant doing background work.

## 4. What Exactly Is the Event Loop?

The event loop is the mechanism inside libuv that repeatedly checks:

* if JavaScript has something to run
* if background work is finished
* if timers are ready
* if network data has arrived
* if callbacks should run now or later

It loops forever while your program is running.

The job of the event loop is simple:

1. Look for work
2. Run it
3. Look again
4. Run more
5. Repeat

This loop is what keeps Node.js responsive, even when waiting for I/O.

## 5. The Life Cycle of a Node.js Async Operation

Let’s take a simple example.

```js
fs.readFile("data.txt", "utf8", () => {
  console.log("File done");
});
console.log("Next task");
```

What actually happens?

Step 1: JavaScript sees `fs.readFile`\
Step 2: It delegates the file reading to libuv\
Step 3: JavaScript keeps running other lines\
Step 4: libuv reads the file in the background\
Step 5: When finished, libuv puts the callback into a queue\
Step 6: When the event loop decides the time is right, JS runs the callback

Output:

```
Next task
File done
```

You can see that Node didn’t “pause” for the file. It delegated and continued.

This delegation is the core behavior of non-blocking Node.js code.

## 6. The Event Loop Runs in Phases

For beginners, we don’t need every micro-detail yet, but a simple overview helps.

The event loop has several phases, and each phase handles specific kinds of callbacks.

A simplified beginner-friendly version:

**Phase A: Timers**\
Runs callbacks from setTimeout and setInterval.

**Phase B: Pending Callbacks**\
Runs some system-level callbacks (not used often in beginner code).

**Phase C: Idle/Prepare**\
Internal use only.

**Phase D: Poll**\
The most important phase.\
This phase waits for new I/O events like:

* file read finished
* network request arrived
* data available on a socket

**Phase E: Check**\
Runs setImmediate callbacks.

**Phase F: Close Callbacks**\
Runs things like socket.on("close", ...).

Every cycle of these phases is one **tick** of the event loop.

For beginners, the key takeaway is:

Each type of callback is placed into the correct queue and the event loop decides _when_ to run them.

## 7. How setTimeout Actually Works

When you run:

```js
setTimeout(() => console.log("Hi"), 1000);
```

What really happens?

* JavaScript registers the timer with libuv
* libuv keeps count of the time
* The callback sits in the “timers phase” queue
* When the time is up, libuv allows the callback to run
* The event loop picks it up when it reaches the timer phase
* JavaScript runs the callback

The timeout is not exact — it means “run _after at least_ X milliseconds.”

If the event loop is busy, the callback may run a bit later.

## 8. How setImmediate Works

setImmediate callbacks go into the “check phase.” They run:

* after I/O is finished
* but before timers run again

They are useful when you want to run something after I/O without waiting for another full event loop cycle.

## 9. How I/O Callbacks Are Scheduled

I/O callbacks (file reading, network events) typically run in the **poll phase**.

This is where Node waits for new messages from the OS.

When data arrives, libuv wakes up and schedules the callback.

This is why Node.js is so efficient with I/O — it uses the OS’s async event notification system rather than checking constantly.

## 10. A Simple Visual Mental Model

Imagine Node.js as a busy restaurant:

JavaScript = the chef\
libuv = kitchen assistants\
Event loop = the manager calling out orders

Flow:

1. Chef receives an order
2. Chef assigns tasks to assistants (prep, cooking, plating)
3. Chef continues cooking other things
4. When assistants finish something, they notify the manager
5. Manager tells the chef “continue this next”
6. Chef picks up the task and finishes it

The chef never waits.\
The chef never stops.\
The chef is always cooking something.

That’s Node.js.

## 11. Why Node.js Is Fast at I/O

Because Node.js doesn’t do the slow work. The OS does.

Node.js simply:

* asks the OS to do it
* continues doing other things
* comes back when the result is ready

This pattern is called **non-blocking I/O**, and it’s the reason Node.js excels at:

* reading files
* handling thousands of HTTP connections
* streaming data
* building proxies
* writing chat servers

The event loop is the heart of this non-blocking behavior.

## 12. Common Beginner Mistake: Blocking the Event Loop

This is important.

Even though Node.js handles background I/O, **your JavaScript code can still block the thread**.

Example of blocking code:

```js
while (true) {}
```

Or:

```js
const data = fs.readFileSync("bigfile.txt");
```

Or:

```js
for (let i = 0; i < 1e10; i++) {}
```

When the event loop is blocked:

* timers won’t run
* I/O callbacks can’t run
* the entire server becomes unresponsive

This is why you must avoid synchronous functions inside request handlers.

## 13. What Beginners Should Remember

You don’t need to memorize all six phases yet.\
Here is what’s important for now:

JavaScript is single-threaded.\
Node.js uses libuv to perform background tasks.\
The event loop decides the next piece of code to run.\
Callbacks from timers, Promises, and I/O arrive at different moments.\
Node.js can handle many tasks because it doesn’t wait for slow work.

This understanding is enough to move forward.

## 14. What We Will Learn Next

The next chapter will explain what actually happens when you run async functions that involve heavy work:

* how Node’s thread pool works
* which functions use the thread pool
* which I/O operations do not block
* why some async functions are truly asynchronous vs simulated async

This will complete your understanding of how Node works under the hood.
