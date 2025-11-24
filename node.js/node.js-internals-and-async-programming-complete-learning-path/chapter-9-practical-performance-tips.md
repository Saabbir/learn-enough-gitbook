---
description: >-
  How to write Node.js applications that stay fast, responsive, scalable, and
  non-blocking — even under heavy load
---

# 📘 CHAPTER 9 — Practical Performance Tips

**ChatGPT said:**

Here is **CHAPTER 9 — Practical Performance Tips**, written in the same style as before:\
no dividers, deeply detailed, beginner-friendly, very clear, and full of practical examples that help you write real-world, non-blocking Node.js applications.

***

## 📘 CHAPTER 9 — Practical Performance Tips

_How to write Node.js applications that stay fast, responsive, scalable, and non-blocking — even under heavy load_

## 1. Introduction

Node.js is fast by design, but speed is not automatic.\
Node’s performance depends heavily on **how you write your code**, especially because JavaScript is single-threaded.

Even if your server has 32 CPUs, your JavaScript runs on only one.\
This means:

one slow task\
one heavy computation\
one giant loop\
one badly chosen API

…can freeze the entire application.

This chapter is all about writing real Node.js code that:

stays responsive\
avoids blocking the event loop\
handles thousands of requests\
manages large data\
uses streams correctly\
uses the thread pool wisely\
scales with load

You will see practical examples, mental models, and clear do’s and don’ts.

Let’s begin.

## 2. Rule #1: Never Block the Event Loop

This is the most important rule in Node.js development.

If you block the event loop:

all clients freeze\
timers stop\
promises pause\
requests queue\
responses slow down\
Node “feels” broken under load

Blocking code includes:

fs.readFileSync\
fs.writeFileSync\
JSON.parse of huge input\
crypto.pbkdf2Sync\
CPU-heavy loops\
image processing in JavaScript\
compression in JavaScript

Avoid all of these inside request handlers.

Always prefer async versions:

fs.readFile\
crypto.pbkdf2\
zlib.gzip\
database queries\
network requests

These delegate work to libuv or the OS.

## 3. Rule #2: Offload CPU-Heavy Tasks

JavaScript is terrible at heavy CPU work because it blocks the main thread.

Examples of CPU-heavy tasks:

large loops (`for (let i = 0; i < 1e10; i++)`)\
sorting huge arrays\
processing millions of objects\
image resizing\
PDF generation\
video encoding\
data compression\
password hashing\
When these run inside JS, everything stops.

Use worker threads for pure JS CPU work:

```js
const { Worker } = require("worker_threads");
new Worker("./worker.js");
```

For non-JS CPU work, use:

thread pool (Node does this automatically for crypto, fs, zlib)\
external services (image servers, media encoders)

## 4. Rule #3: Use Streams for Large Data

Reading or writing large files with fs.readFile or fs.writeFile is inefficient.

If you read a 2GB file:

fs.readFile loads all 2GB into memory\
Node risks crashing\
your server becomes slow

Use streams:

```js
fs.createReadStream("big.zip").pipe(res);
```

Streams:

use tiny memory\
handle backpressure\
transfer data efficiently\
work perfectly with HTTP

Use streams for:

video streaming\
file downloads\
log processing\
real-time audio

## 5. Rule #4: Manage Backpressure Properly

Backpressure stops fast writers from overwhelming slow readers.

If you write too fast:

memory grows\
buffers overflow\
Node slows down

The pipe() method handles backpressure automatically:

```js
readable.pipe(writable);
```

If you manually write:

```js
if (!stream.write(chunk)) {
  stream.once("drain", () => {
    // continue writing
  });
}
```

Always check return values.

## 6. Rule #5: Never Use JSON.parse on Untrusted Large Input

Parsing huge JSON blocks the event loop.

Requests freeze.\
API latency spikes.\
Server becomes unresponsive.

Better strategies:

limit request body size\
use streaming JSON parsers\
move parsing to worker threads\
validate JSON **before** parsing

## 7. Rule #6: Avoid Async Inside Loops (Use Promise.all or batching)

Bad:

```js
for (const user of list) {
  await sendEmail(user);
}
```

Runs one by one. Slow.

Better:

```js
await Promise.all(list.map(sendEmail));
```

Runs in parallel.

If list is huge, batch it:

```js
const batchSize = 20;
for (let i = 0; i < list.length; i += batchSize) {
  const batch = list.slice(i, i + batchSize);
  await Promise.all(batch.map(sendEmail));
}
```

This avoids overwhelming external services.

## 8. Rule #7: Use Caching for Expensive Operations

Examples of expensive operations:

database queries\
API requests\
file reads\
crypto operations\
JSON loading

Use caching strategies:

in-memory (Map, LRU cache)\
Redis\
CDN for static content\
memoization

For example:

```js
const cache = new Map();

async function getUser(id) {
  if (cache.has(id)) return cache.get(id);

  const user = await db.query("...");
  cache.set(id, user);
  return user;
}
```

Memory caching saves time but increases memory usage.\
Use wisely.

## 9. Rule #8: Use Connection Keep-Alive

Without keep-alive:

each HTTP request = new TCP connection\
costly\
slow\
wasteful

Node enables keep-alive automatically for incoming requests.

For outgoing requests, enable it:

```js
const agent = new http.Agent({ keepAlive: true });
```

Reuse connections for:

database clients\
API clients\
microservices

This massively improves performance.

## 10. Rule #9: Avoid Highly Recursive Code

Recursive functions can:

overflow the call stack\
use too much memory\
block the thread

Prefer:

iterative loops\
tail-call optimization (not supported in Node)\
divide-and-conquer with worker threads

Example of bad recursion:

```js
function fib(n) {
  if (n < 2) return n;
  return fib(n - 1) + fib(n - 2);
}
```

Better:

use memoization\
or run it in a worker thread\
or use an algorithmic improvement

## 11. Rule #10: Prefer Fast JSON Serialization Libraries When Needed

Native JSON.stringify is fast but blocks the event loop for large objects.

For extremely large objects or performance-critical systems, use:

fast-json-stringify\
safe-stable-stringify

Better yet: avoid sending massive JSON altogether.

## 12. Rule #11: Watch Out for Memory Leaks

Common sources of memory leaks:

global arrays\
never-cleared Maps\
caches that grow indefinitely\
listeners not removed\
promises never resolved\
large buffers kept in memory

Use tools like:

Chrome DevTools\
Node profiler\
heap snapshots

Example of leak:

```js
const cache = {};

function add(item) {
  cache[item.id] = item; // never deleted
}
```

## 13. Rule #12: Avoid Large Logging in Production

Huge logs:

slow down disk\
block thread pool\
cause log rotation delays

Use asynchronous logging:

pino\
winston\
bunyan

Tip:

write logs to a file through a stream:

```js
const logStream = fs.createWriteStream("app.log");
logStream.write("Hello\n");
```

## 14. Rule #13: Use Timeouts Everywhere

Never trust external resources.

Add timeouts for:

HTTP requests\
database queries\
file operations\
promises that may hang

Example:

```js
function withTimeout(promise, ms) {
  return Promise.race([
    promise,
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error("Timeout")), ms)
    )
  ]);
}
```

Timeouts prevent stuck requests and memory leaks.

## 15. Rule #14: Use Cluster or Load Balancers for Multi-Core Scaling

Node runs on a single thread, but your server has many cores.

Use:

PM2\
Node’s cluster module\
Docker + Kubernetes\
NGINX

This allows multi-core scaling:

core 1 → Node instance 1\
core 2 → Node instance 2\
core 3 → Node instance 3

This increases concurrency dramatically.

## 16. Rule #15: Monitor Event Loop Lag

Event loop lag indicates blocking.

To measure:

```js
const start = Date.now();
setImmediate(() => {
  console.log("Lag:", Date.now() - start);
});
```

If lag is high:

your code contains blocking operations\
thread pool is overloaded\
JSON parsing too large\
CPU tasks inside JS\
streams not used well

Fix blocking code immediately.

## 17. Summary

You now know how to write Node.js applications that remain fast and responsive:

never block the event loop\
use async APIs\
move CPU-heavy tasks to worker threads\
use streams for large data\
handle backpressure correctly\
avoid large JSON operations\
batch and parallelize tasks efficiently\
cache expensive operations\
use keep-alive for networking\
avoid memory leaks\
use logging properly\
set timeouts everywhere\
scale with cluster/load balancers\
monitor event loop lag

These techniques are used by high-performance Node.js applications at companies like Netflix, Uber, LinkedIn, and PayPal.
