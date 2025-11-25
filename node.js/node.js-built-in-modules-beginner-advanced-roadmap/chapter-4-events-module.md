---
description: >-
  Understanding EventEmitter, custom events, internal events, and how Node.js
  apps communicate
---

# 📘 CHAPTER 4 — Events Module

## 1. Introduction

Node.js is built on the idea of **events**.

Every time you:

receive an HTTP request\
read a file with a callback\
listen for data on a stream\
watch for a timer\
handle a socket connection\
wait for process exit

…you are actually responding to events.

Events make Node.js feel alive.\
They allow your application to react to things happening over time instead of executing everything at once.

This chapter explains how Node’s event-driven pattern works and how to use the **events** module to build scalable apps.

## 2. What Is Event-Driven Programming?

Event-driven programming means:

your code doesn’t run in one long sequence\
instead, your code _waits_ for things to happen\
and responds only when they do

Imagine a busy restaurant:

the chef waits for new orders\
the waiters shout “Order up!”\
the chef reacts when called\
the kitchen runs on events

Node.js works the same way.

Events allow Node to stay fast and responsive, especially during I/O.

## 3. The EventEmitter Class

The heart of event-driven programming in Node is the **EventEmitter** class.

Every major Node component is an EventEmitter:

http.Server\
streams (readable and writable)\
net.Socket\
fs streams\
process\
child processes\
worker threads\
even timers

You will use EventEmitter constantly — sometimes directly, often indirectly.

To use it:

```js
const EventEmitter = require("events");
const emitter = new EventEmitter();
```

Now you can emit events and listen for them.

## 4. Listening for Events

Use `.on()` to listen for events.

```js
emitter.on("greet", () => {
  console.log("Hello there!");
});
```

Now emit the event:

```js
emitter.emit("greet");
```

Output:

```
Hello there!
```

Events can carry data too:

```js
emitter.on("greet", name => {
  console.log(`Hello, ${name}`);
});

emitter.emit("greet", "Saabbir");
```

## 5. emit() — Triggering an Event

emit() is how you _signal_ that something happened.

emit() does two things:

1. finds all listeners for that event
2. calls them one by one

Example:

```js
emitter.emit("data", "chunk1");
```

If no one is listening, emit() silently does nothing (except one special case: "error").

## 6. once() — Only Listen One Time

Sometimes you want a listener that runs just once:

```js
emitter.once("ready", () => {
  console.log("This will only run once");
});
```

Even if you emit the event multiple times:

```js
emitter.emit("ready");
emitter.emit("ready");
```

Output:

```
This will only run once
```

Common use cases:

initialization steps\
authentication hooks\
one-time callbacks

## 7. Removing Event Listeners

You can remove listeners using:

removeListener\
off (alias)\
removeAllListeners

Example:

```js
function handler() {
  console.log("hello");
}

emitter.on("greet", handler);
emitter.off("greet", handler);
```

Removing listeners is important when:

building long-running servers\
using streams\
preventing memory leaks\
detaching temporary listeners

## 8. The Special "error" Event

One event behaves differently:

```js
emitter.emit("error", new Error("Oops"));
```

If you emit an "error" event but do NOT have a listener for it, Node will **crash**.

To avoid this:

```js
emitter.on("error", err => {
  console.log("Handled:", err.message);
});
```

This is how many internal Node modules handle failure.

## 9. Getting the Number of Listeners

You can check how many listeners are attached:

```js
emitter.listenerCount("greet");
```

Good for debugging or ensuring you don’t attach too many listeners.

## 10. Real-World Use Case 1: Custom Logger

You can build your own logger:

```js
class Logger extends EventEmitter {
  log(msg) {
    this.emit("message", msg);
  }
}

const logger = new Logger();

logger.on("message", msg => {
  console.log("Log:", msg);
});

logger.log("Hello world");
```

This pattern is extremely common in real applications.

## 11. Real-World Use Case 2: Order Processing Flow

Imagine an e-commerce backend:

new order placed → emit("order created")\
order paid → emit("payment succeeded")\
items shipped → emit("shipment sent")

Event-driven flow:

```js
emitter.on("order", order => {
  console.log("Order received:", order.id);
});

emitter.emit("order", { id: 123 });
```

This creates clean, decoupled code.

## 12. Real-World Use Case 3: Task Queue

Events can be used to build simple queues:

```js
const queue = new EventEmitter();

queue.on("task", data => {
  console.log("Processing:", data);
});

queue.emit("task", { id: 1 });
queue.emit("task", { id: 2 });
```

Each event is like a background job.

## 13. Internal Node.js Events

Many common Node APIs are EventEmitters under the hood.

Examples:

HTTP Server:

```js
server.on("request", (req, res) => {});
```

Streams:

```js
readable.on("data", chunk => {});
readable.on("end", () => {});
```

Child Processes:

```js
child.on("exit", code => {});
```

net.Socket:

```js
socket.on("connect", () => {});
```

process:

```js
process.on("exit", code => {});
```

Even timers use events internally.

## 14. Emitting Events Asynchronously

emit() is synchronous — listeners run immediately.

If you want async events:

```js
setImmediate(() => {
  emitter.emit("update");
});
```

Or use process.nextTick:

```js
process.nextTick(() => emitter.emit("update"));
```

This is useful when you want to:

allow other code to run first\
avoid blocking\
queue events

## 15. Maximum Listener Limit

Node warns you if you attach too many listeners:

```
MaxListenersExceededWarning
```

Default limit: **10 listeners per event**.

You can increase it:

```js
emitter.setMaxListeners(50);
```

Useful in large applications.

## 16. Summary

You now understand how to use Node’s events module:

EventEmitter\
on(), once(), emit()\
removing listeners\
error events\
listener counts\
synchronous vs asynchronous events\
real-world use cases\
internal events in Node\
custom event-driven architectures

Events are one of the most important concepts in Node.js.\
They define how Node handles I/O, servers, streams, and communication.

You will use events everywhere — especially when working with streams, HTTP servers, sockets, and child processes later in the series.
