---
description: >-
  How Node.js uses multiple CPU cores, runs parallel work, and isolates
  expensive tasks without blocking the event loop
---

# 📘 CHAPTER 10 — Worker Threads, Child Processes & Clustering

## 1. Introduction

You’ve learned that JavaScript runs on a single thread.\
This single thread is perfect for I/O, but terrible for:

heavy CPU work\
image processing\
video encoding\
PDF generation\
large data transformations\
cryptography done in JS\
mathematical simulations

These tasks freeze the event loop and block everything.

To solve this, Node.js gives us **three powerful tools**:

Worker Threads\
Child Processes\
Clustering

Each one solves a different problem.\
Each one gives Node more power.\
Each one helps you scale across CPU cores or isolate work so the main thread stays free.

Let’s explore them one by one, but in a way that is very easy to understand.

## 2. Why One Thread Is Not Enough for Some Tasks

JavaScript’s single thread is amazing for I/O because:

fast event loop\
perfect non-blocking pattern\
OS handles the slow stuff

But JavaScript is **not** good at CPU tasks.

For example:

```js
for (let i = 0; i < 1e10; i++) {}
```

This will freeze:

HTTP requests\
streams\
timers\
promises\
the entire server

Because the event loop is stuck doing math.

You need a way to move heavy work away from the main thread.

This is where our three tools come in.

## 3. Worker Threads (Run JavaScript in Parallel)

Worker Threads allow you to run JavaScript in **true parallel threads**.

Key idea:

The main thread runs your server logic\
A worker thread runs CPU-heavy JS\
Both run simultaneously\
The worker does not block the main event loop

Workers are great for:

image processing\
PDF generation\
JSON parsing of huge strings\
machine learning tasks\
sorting huge arrays\
mathematical simulations\
cryptography done in JS

Here is the simplest worker example:

main.js:

```js
const { Worker } = require("worker_threads");

const worker = new Worker("./worker.js");

worker.on("message", msg => {
  console.log("Message from worker:", msg);
});

worker.postMessage("start");
```

worker.js:

```js
const { parentPort } = require("worker_threads");

parentPort.on("message", msg => {
  let total = 0;
  for (let i = 0; i < 1e9; i++) total += i;
  parentPort.postMessage(total);
});
```

The worker runs the loop.\
The main thread stays free.

Workers communicate using:

messages\
buffers\
shared memory

Workers do **not** share variables with the main thread.\
Each worker has its own V8 instance.

## 4. When Should You Use Worker Threads?

Use workers when:

a task is CPU-heavy\
you want to avoid blocking the event loop\
you want concurrency inside one Node process\
you need multiple threads for pure JS work\
you need shared memory between threads

Do not use workers for:

small tasks (overhead is too high)\
I/O tasks (use async I/O instead)\
network requests (use event loop + async I/O)

Workers are the right tool for **CPU-bound JS**.

## 5. Child Processes (Run External Programs)

Worker threads run JavaScript.\
Child processes run **any program**, like:

Python scripts\
FFmpeg (video processing)\
ImageMagick\
system commands\
bash scripts\
other Node processes

Child processes allow you to use other tools for heavy tasks.

Example:

```js
const { exec } = require("child_process");

exec("ls -la", (err, stdout, stderr) => {
  console.log(stdout);
});
```

Or run a Python script:

main.js:

```js
const { fork } = require("child_process");
const child = fork("script.py");
```

Child processes are fully separated:

they have their own memory\
they have their own threads\
they run independently\
they will not block your event loop

But they have more overhead than worker threads.

## 6. The Three Child Process Methods

exec\
Runs a command in a shell\
Buffers entire output\
Not good for large output

execFile\
Runs a program directly (no shell)\
More efficient

spawn\
Streams input/output\
Perfect for large output\
Perfect for long-running tasks

Example using spawn:

```js
const { spawn } = require("child_process");

const child = spawn("ping", ["google.com"]);

child.stdout.on("data", data => {
  console.log(data.toString());
});
```

This streams output without storing everything in memory.

## 7. When Should You Use Child Processes?

Use child processes for:

running external programs\
encoding videos\
image processing via external tools\
FFmpeg\
sharp\
Python scripts\
heavy shell commands\
parallel Node.js scripts

Do not use child processes when:

you only need JS computations\
you need quick communication

Workers are usually better for pure JS.

## 8. Clustering (Scale Node.js Across CPU Cores)

Node uses a single CPU core.

But servers usually have:

4 cores\
8 cores\
16 cores\
32 cores

Running Node on one core wastes the others.

Clustering allows you to run **multiple Node processes**, one per core.

Each process receives connections.\
A master process balances traffic.\
Every worker process runs your Node.js server logic.

Example:

```js
const cluster = require("cluster");
const http = require("http");
const os = require("os");

if (cluster.isMaster) {
  const numCPUs = os.cpus().length;
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }
} else {
  http.createServer((req, res) => {
    res.end("Handled by " + process.pid);
  }).listen(3000);
}
```

If your machine has 8 CPUs:

Node runs 8 worker processes\
each one handles some requests

This allows Node to handle 8 times more concurrency.

## 9. When Should You Use Clustering?

Use clustering when:

you want to use all CPU cores\
your API handles many requests\
you need horizontal scaling inside one server

Clustering is good for:

web servers\
proxy servers\
API gateways

Do not use clustering for:

CPU-heavy tasks inside JS (use workers instead)\
running external programs (use child processes)

## 10. Putting All Three Together (The Perfect Architecture)

If your server handles high traffic and heavy computation, combine these:

Clustering → use all CPU cores\
Worker Threads → handle CPU-heavy JS work\
Child Processes → run external tools (e.g., videos, images)

Example real-world usage:

Node handles HTTP traffic\
When user uploads an image → pass image to worker thread\
Worker thread resizes image\
If video is uploaded → pass to FFmpeg via child process\
Cluster duplicates the server across 8 CPU cores

Everything remains non-blocking.

## 11. Choosing the Right Tool

CPU-heavy JavaScript → Worker Threads\
External programs or scripts → Child Processes\
Scaling Node across CPU cores → Cluster\
High traffic + CPU work → Cluster + Workers\
Large file conversions → Child Processes\
Parallel background work → Workers\
Streaming huge outputs → Child Processes (spawn)

This table summarizes:

| Use Case                            | Best Tool      |
| ----------------------------------- | -------------- |
| Heavy JS computation                | Worker Threads |
| Running FFmpeg                      | Child Process  |
| Running Python script               | Child Process  |
| Using all CPU cores                 | Cluster        |
| Parallel workers inside one process | Worker Threads |
| Large output streaming              | spawn()        |
| File compression by external tool   | Child Process  |
| CPU-bound JSON processing           | Worker Thread  |

## 12. Summary

You learned how Node goes beyond its single-threaded JavaScript model by using three powerful tools:

Worker Threads\
run JavaScript in parallel threads\
perfect for CPU-heavy JS work

Child Processes\
run external programs\
perfect for video, image, Python, and system-level processing

Clustering\
run multiple Node.js processes\
use all CPU cores\
perfect for scaling servers

These tools help you build applications that are:

fast\
responsive\
non-blocking\
scalable\
fault-tolerant

You now understand how to push Node.js beyond a single thread and use the full power of your machine.
