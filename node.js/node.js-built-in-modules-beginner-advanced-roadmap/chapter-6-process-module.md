---
description: >-
  Understanding environment variables, arguments, stdout, stderr, exit events,
  memory usage, nextTick, and how Node.js controls your running program
---

# 📘 CHAPTER 6 — PROCESS Module

**ChatGPT said:**

Here is **📘 CHAPTER 6 — PROCESS Module**, written in the same style as your earlier chapters:\
clear, beginner-friendly, deeply detailed, natural storytelling tone, and **no section dividers**.

***

## 📘 Node.js Built-In Modules

## Chapter 6 — PROCESS Module

_Understanding environment variables, arguments, stdout, stderr, exit events, memory usage, nextTick, and how Node.js controls your running program_

## 1. Introduction

Every Node.js application interacts with the **process** object — even if you’ve never touched it directly.

The process module gives your script access to:

environment variables\
command-line arguments\
the current working directory\
uptime\
stdout/stderr\
signals (like Ctrl+C)\
exit events\
memory usage\
Node version\
architecture\
high-resolution timers\
And most importantly:\
low-level control over how your program runs.

Unlike many other Node.js modules, you **don’t need to import** it:

```js
console.log(process);
```

process is a **global object**, always available.

This chapter will teach you everything you need about the process module — one of the most essential tools for building CLI tools, servers, dev scripts, monitoring dashboards, and production-grade backend systems.

## 2. process.env — Environment Variables

Environment variables allow you to configure your application **without hardcoding values**.

Common examples:

PORT\
DATABASE\_URL\
API\_KEY\
NODE\_ENV

Access them using:

```js
console.log(process.env.PORT);
console.log(process.env.NODE_ENV);
```

Setting environment variables:

macOS/Linux:

```
PORT=5000 node app.js
```

Windows (PowerShell):

```
$env:PORT=5000; node app.js
```

Defaults are common:

```js
const PORT = process.env.PORT || 3000;
```

NODE\_ENV is widely used:

development\
production\
test

Example:

```js
if (process.env.NODE_ENV === "production") {
  console.log("Running in production mode");
}
```

Environment vars are critical for deployment.

## 3. process.argv — Command-Line Arguments

process.argv contains everything passed to your Node.js script.

Example:

```
node app.js hello world
```

argv contains:

\[\
path/to/node,\
path/to/app.js,\
"hello",\
"world"\
]

Example usage:

```js
const args = process.argv.slice(2);
console.log(args);
```

This lets you build CLI tools:

```
node greet.js Saabbir
```

```js
const name = process.argv[2];
console.log("Hello", name);
```

Many CLI libraries (yargs, commander) are built on top of this.

## 4. process.stdout and process.stderr

stdout → normal program output\
stderr → errors and diagnostics

Printing text:

```js
process.stdout.write("Hello\n");
process.stderr.write("An error occurred\n");
```

These are streams, which means you can pipe them:

```js
stream.pipe(process.stdout);
```

Logging tools and CLI tools use stdout/stderr heavily.

## 5. process.cwd() — Current Working Directory

This returns where Node was _started from_:

```js
console.log(process.cwd());
```

Important note:\
cwd is different from `__dirname`.

\_\_dirname = directory of the current file\
cwd = directory where you ran the command

Example:

```
node scripts/run.js
```

Inside run.js:

\_\_dirname = "/project/scripts"\
cwd = "/project"

This difference is extremely important when reading files.

## 6. process.chdir() — Change Working Directory

You can change directory programmatically:

```js
process.chdir("/tmp");
```

Useful for:

scripts\
build tools\
automations

But be careful: changing directories affects all relative paths.

## 7. process.exit() — Exit the Program

Exit with success:

```js
process.exit(0);
```

Exit with failure:

```js
process.exit(1);
```

Exit codes help other systems detect success or failure.

You should avoid calling process.exit() inside HTTP handlers or streams because it stops everything immediately.

## 8. process.on("exit") — Program Is About to End

Listen for exit:

```js
process.on("exit", code => {
  console.log("Exiting with code:", code);
});
```

This event is synchronous — you cannot perform async code here:

No setTimeout\
No promises\
No file reads

Only synchronous cleanup is allowed.

## 9. process.on("SIGINT") — Handling Ctrl + C

This lets your program gracefully shut down when the user presses Ctrl+C.

```js
process.on("SIGINT", () => {
  console.log("Shutting down gracefully…");
  process.exit();
});
```

Useful for:

servers\
CLIs\
databases\
long-running tasks

Other signals:

SIGTERM (docker, systemd)\
SIGUSR1\
SIGUSR2

These allow you to integrate with container orchestration.

## 10. process.memoryUsage()

Returns memory usage details:

```js
console.log(process.memoryUsage());
```

Example structure:

```
{
  rss: 23445504,
  heapTotal: 6137344,
  heapUsed: 3987345,
  external: 123456
}
```

heapUsed grows when you create big objects\
rss grows with Buffers and native resources

Useful for detecting memory leaks.

## 11. process.uptime()

How long the Node process has been running:

```js
console.log(process.uptime());
```

Used in monitoring dashboards.

## 12. process.hrtime() — High-Resolution Timer

Used for measuring performance:

```js
const start = process.hrtime();

setTimeout(() => {
  const diff = process.hrtime(start);
  console.log(diff); // [seconds, nanoseconds]
}, 1000);
```

This is far more accurate than Date.now().

perf\_hooks module builds on this.

## 13. process.version and process.versions

Get Node version:

```js
console.log(process.version); 
// e.g. "v20.11.0"
```

Get versions of underlying components (V8, libuv, etc.):

```js
console.log(process.versions);
```

Useful for debugging, profiling, or CI/CD checks.

## 14. process.nextTick() — Microtask Queue

nextTick() schedules a callback to run:

before the event loop continues\
before promises\
before I/O callbacks

Example:

```js
process.nextTick(() => {
  console.log("Runs before promise callbacks");
});
```

Order:

synchronous code\
nextTick queue\
microtasks (Promise.then)\
event loop phases

Use nextTick thoughtfully — overusing it can stall the event loop.

## 15. process.emitWarning()

You can emit custom warnings:

```js
process.emitWarning("Deprecated method used");
```

You can categorize them:

```js
process.emitWarning("Something bad", {
  code: "MY_WARNING",
  detail: "More information here"
});
```

Warnings show nicely in console with stack traces.

## 16. Real-World Examples

Environment-based configuration:

```js
if (process.env.NODE_ENV === "production") {
  enableCaching();
}
```

Passing arguments to scripts:

```
node backup.js --force
```

Graceful shutdown:

```js
process.on("SIGTERM", () => {
  server.close(() => process.exit(0));
});
```

Checking memory before processing a large file:

```js
if (process.memoryUsage().heapUsed > 200_000_000) {
  console.log("Too much memory in use");
}
```

Measuring performance:

```js
const start = process.hrtime();
runTask();
const diff = process.hrtime(start);
console.log(diff);
```

Creating CLI tools:

```js
console.log("Arguments:", process.argv.slice(2));
```

## 17. Common Mistakes Beginners Make

Using process.exit() too early — ends the entire app\
Confusing cwd() with \_\_dirname\
Expecting async operations inside process.on("exit")\
Not handling SIGINT/SIGTERM in long-running servers\
Forgetting that environment variables are strings\
Overusing nextTick (causes starvation)\
Parsing command-line arguments manually instead of libraries

## 18. Summary

You now understand the process module:

environment variables\
command-line arguments\
stdout and stderr\
cwd and chdir\
exit codes and signals\
uptime and memory usage\
Node version information\
high-resolution timers\
nextTick microtask behavior\
custom warnings

The process module is essential when building:

CLI applications\
servers\
dev automation tools\
infrastructure scripts\
deployment systems\
monitoring dashboards

Knowing how process works gives you deeper control over your Node.js application’s behavior — both in development and in production.
