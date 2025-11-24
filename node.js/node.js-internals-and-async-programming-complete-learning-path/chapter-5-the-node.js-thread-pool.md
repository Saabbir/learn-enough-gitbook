---
description: How Node does heavy work in the background without blocking the event loop
---

# 📘 CHAPTER 5 — The Node.js Thread Pool

## 1. Introduction

By now you understand:

* JavaScript is single-threaded
* Node.js is asynchronous
* Some tasks block, some don’t
* The event loop decides when callbacks run

But one huge question remains:

If JavaScript only has one thread, **how does Node run slow operations without blocking?**

The answer is the Node.js **thread pool** — a group of background workers inside libuv that handle intensive tasks for you.

This chapter explains:

* what the thread pool is
* why Node needs it
* which functions use it
* how it prevents blocking
* how to expand it
* how it interacts with the event loop
* when performance issues occur
* practical examples of thread pool use

This topic is the missing link in understanding Node internals.

## 2. Why Does Node Need a Thread Pool?

Node is great at handling I/O, because the OS can notify node when something happens (like a file finishes reading or network data arrives). That doesn’t require extra threads.

However, not all operations can be handled purely by the operating system. Some things require actual CPU work:

* hashing passwords
* doing encryption
* compressing data
* reading files
* writing files
* DNS lookups (some types)
* zlib operations
* some fs operations

These tasks are slow and can’t be run on the main JavaScript thread.

If JavaScript runs them directly, it will block the entire application.

So Node delegates heavy tasks to a **thread pool** — a set of worker threads maintained by libuv.

## 3. What Exactly Is the Thread Pool?

The thread pool is a collection of background threads managed by libuv.

By default, it contains **4 threads**.

These threads are used to execute heavy work outside the main JavaScript thread.

Important detail:

These are **not** JavaScript threads.\
They are C/C++ worker threads inside Node’s engine.

JavaScript never runs in multiple threads by itself.\
But Node can run heavy background tasks in multiple threads.

This is the secret behind Node’s asynchronous power.

## 4. How the Thread Pool Works (Beginner-Friendly Explanation)

Imagine Node.js like a restaurant:

JavaScript thread = the head chef\
Thread pool = four assistants

If the chef receives an order that takes a long time (e.g., preparing a complicated sauce), instead of doing it himself and stopping the whole kitchen, he sends it to an assistant.

Meanwhile, the chef continues cooking other dishes.

When the assistant finishes, they notify the chef, who then completes the order.

Now let’s map this to Node:

JavaScript receives a slow task → delegates it to the thread pool → keeps running other code → thread finishes → event loop schedules callback → JavaScript executes it

That’s the entire mechanism.

## 5. Which Node.js Functions Use the Thread Pool?

Many built-in modules use the thread pool behind the scenes.

These include:

File system (fs)

* fs.readFile
* fs.writeFile
* fs.stat
* fs.unlink
* fs.readdir\
  Almost every “fs.\*” async function uses the thread pool.

Crypto operations

* crypto.pbkdf2
* crypto.scrypt
* crypto.randomBytes (sometimes)
* crypto.sign
* crypto.verify

Zlib (compression)

* zlib.deflate
* zlib.gzip
* zlib.inflate
* zlib.unzip

DNS (some methods)

* dns.lookup (on some OSes)

Anything CPU heavy

* image processing
* JSON parsing of huge objects
* PDF generation
* file compression

All of these avoid blocking thanks to the thread pool.

## 6. Which Functions DO NOT Use the Thread Pool?

Network operations usually do **not** use the thread pool.

Why?

Because the operating system handles them asynchronously using kernel-level notification systems like:

* epoll (Linux)
* kqueue (BSD/macOS)
* IOCP (Windows)

Examples that do NOT use the thread pool:

* HTTP requests
* TCP sockets
* UDP sockets
* TLS handshakes (partially)
* Most network I/O

These are handled directly by the OS and event loop.

This is why network servers in Node.js are extremely fast.

## 7. Step-by-Step Example of How Thread Pool Handles a Task

Let’s study PBKDF2 hashing:

```js
const crypto = require("crypto");

crypto.pbkdf2("password", "salt", 100000, 64, "sha512", () => {
  console.log("Hash done");
});

console.log("Next task");
```

What actually happens internally?

1. JavaScript sees `crypto.pbkdf2`
2. JavaScript immediately returns control to the event loop
3. The hashing job is sent to a thread in the thread pool
4. JavaScript continues running “Next task”
5. The thread does the slow hashing work
6. When finished, libuv pushes the callback into the event loop
7. Event loop delivers the callback
8. JavaScript prints “Hash done”

The main thread never waits.\
The heavy work happens in parallel.

This is Node’s magic.

## 8. The Thread Pool Has a Limit (Default: 4)

By default, the thread pool has **4 workers**.

This means:

* only 4 thread-pool tasks can run at the same time
* the 5th task must wait in a queue

Example:

If you hash 100 passwords at once:

```js
for (let i = 0; i < 100; i++) {
  crypto.pbkdf2("p", "s", 100000, 64, "sha512", () => {
    console.log("Done", i);
  });
}
```

Only 4 will run in parallel.

The remaining 96 wait.

If hashing is slow, this can create a bottleneck.

## 9. Increasing the Thread Pool Size

You can increase the number of worker threads by setting:

```js
process.env.UV_THREADPOOL_SIZE = 8;
```

This must be done **before** any async tasks run.

You can increase it up to 128 threads.

Example:

```js
process.env.UV_THREADPOOL_SIZE = 16;
const crypto = require("crypto");
```

More threads help only if your code relies heavily on thread-pool operations.

But be careful:

* more threads = more CPU usage
* too many threads = overhead
* each thread uses memory

For most applications, the default of 4 is fine.

## 10. Thread Pool is NOT the Same as Worker Threads

This is important.

Thread pool:

* used internally by Node
* does not run JavaScript
* used for FS/crypto/zlib
* invisible to your code

Worker threads:

* used by developers intentionally
* run JavaScript in parallel
* separate execution environments
* good for CPU-heavy JS

Thread pool = invisible helpers\
Worker threads = explicitly created by you

We will cover worker threads later in the advanced chapters.

## 11. When the Thread Pool Becomes a Bottleneck

If your application does:

* many crypto.pbkdf2 operations
* many file system operations
* many compression tasks

…the thread pool can become saturated.

Symptoms:

* requests are slow even though CPU is idle
* tasks wait in a long queue
* your Node server seems “busy”
* event loop is free but tasks don’t finish

This usually happens in:

* authentication servers hashing many passwords
* servers compressing files on the fly
* video processing apps
* heavy filesystem automation scripts

Solutions:

* increase UV\_THREADPOOL\_SIZE
* use worker threads for CPU-heavy JS
* offload work to message queues
* use specialized external services

## 12. Real Example: Slow PBKDF2 Can Freeze Your API

Imagine a login API that hashes passwords using pbkdf2.

If 4 users login at the same time:

* thread pool is full
* the 5th login must wait
* the API slows down

If 50 users login at the same time:

* huge queue builds up
* event loop looks free, but responses take long
* your server becomes unresponsive

This is why authentication servers need careful architecture.

## 13. Real Example: Slow File System Operations

If your API uses fs.readFile too much:

* every operation goes to the thread pool
* thread pool fills up
* file reads become slow
* everything bottlenecks

Better solution:

Use streams instead of reading entire files.

## 14. Summary

You now deeply understand:

what the thread pool is\
why Node.js needs it\
how it prevents blocking\
which operations use it\
which do not use it\
how parallelism works behind the scenes\
why the default size is 4\
when to increase it\
how saturation causes slowdowns\
the difference between thread pool and worker threads\
how Node.js handles heavy work without blocking the event loop

This chapter is essential knowledge for backend developers and anyone building performance-critical applications.
