---
description: >-
  The most beginner-friendly explanation of how Node.js schedules every
  callback, timer, I/O event, microtask, and nextTick task.
---

# 📘 CHAPTER 6 — Deep Event Loop Internals (libuv Phases Explained Simply)

## 1. Introduction

By now you know:

JavaScript is single-threaded\
Node.js uses libuv for background work\
The event loop decides when callbacks run\
Microtasks run before macrotasks

But to truly understand Node.js, you need to know **how the event loop actually works** at the libuv level.\
This is where most developers get overwhelmed. The documentation feels too theoretical, diagrams look intimidating, and the explanations are often targeted at advanced C++ developers.

So in this chapter, we break everything down into:

simple language\
intuitive mental models\
real examples\
step-by-step scheduling\
practical explanations of each phase

By the end, you’ll be able to mentally trace exactly how Node runs timers, network callbacks, file I/O, setImmediate, nextTick, promises, and more.

Let’s begin with the big picture.

## 2. The Event Loop Is a Repeating Cycle

The event loop runs in cycles (called _ticks_).

Each cycle goes through different phases.

Each phase handles a specific type of callback.

The simplified version you learned earlier was:

1. Timers
2. I/O callbacks
3. Poll
4. Check
5. Close callbacks

But the full version is slightly more detailed, and we’ll explore each phase at a gentle pace.

The important thing to understand:

Each phase has its own queue of callbacks waiting to be executed.

As the event loop moves from phase to phase, it checks:

“Are there callbacks in this queue? If yes, run them.”

Then it moves on.

## 3. The Six Official libuv Event Loop Phases

libuv has six main phases:

1. timers
2. pending callbacks
3. idle
4. prepare
5. poll
6. check
7. close callbacks

There’s also microtasks and process.nextTick, which run between phases.

Let’s understand each phase without confusion.

## 4. Phase 1: Timers Phase

This is where callbacks from setTimeout and setInterval run.

When you write:

```js
setTimeout(() => console.log("timer"), 1000);
```

Node schedules the callback to run **no earlier than 1000ms**, but not exactly at 1000ms.

Once the event loop returns to the “timers phase,” Node checks:

“Is the timer due?”\
If yes → run callback\
If no → skip it for now

Important detail:\
Timeout is not guaranteed, because if Node is busy, the callback runs later.

## 5. Phase 2: Pending Callbacks Phase

This phase is rarely used by application code.

It handles some system-level callbacks like:

* TCP errors
* DNS resolver errors
* Some retries

You typically won’t interact with this phase directly, but knowing it exists helps complete the picture.

## 6. Phase 3: Idle Phase

This is an internal phase used by libuv for bookkeeping tasks.\
Nothing user-facing runs here.\
You can think of it as “housekeeping time.”

## 7. Phase 4: Prepare Phase

Also used internally by libuv.\
It sets up the environment before the poll phase.

Again, not something you interact with.

## 8. Phase 5: Poll Phase (The Most Important Phase)

This is the heart of the event loop.

This is where Node spends most of its time.

During this phase, Node:

* waits for new I/O events
* runs callbacks from completed I/O
* decides when to move to the next phase

Example I/O callbacks:

* fs.readFile finished
* http request arrived
* socket data ready
* DNS lookup finished

This phase behaves differently depending on whether callbacks are available.

Case 1: There are callbacks\
→ run them

Case 2: There are no callbacks\
→ Node may wait for new I/O\
→ or if timers are due, move on

Think of poll as Node’s “waiting room” where it listens for OS events.

## 9. Phase 6: Check Phase (Runs setImmediate)

Callbacks from setImmediate run here.

Example:

```js
setImmediate(() => console.log("immediate"));
```

setImmediate does NOT run in the timers phase.

It runs in the check phase, which is after poll.

This means:

setImmediate tends to run before setTimeout 0,\
but not always (depends on timing).

We’ll see examples later.

## 10. Phase 7: Close Callbacks Phase

This phase handles:

```js
socket.on("close", () => {});
```

or resource cleanup callbacks.

Rarely used directly by beginners.

## 11. Microtasks Run Between Phases

This is extremely important.

Microtasks (Promise callbacks, queueMicrotask) run:

* after every callback
* at the end of each phase
* before Node moves to the next phase

Example:

```js
Promise.resolve().then(() => console.log("promise"));
```

This will run **before** any macrotask callback scheduled in the next phase.

Node's rule:

After running ANY callback, run microtasks before continuing.

This is why promises often run earlier than expected.

## 12. process.nextTick Runs Before Microtasks

Node has its own high-priority queue: nextTick.

Order of highest priority:

1. current call stack
2. process.nextTick queue
3. microtask queue
4. next event loop phase

nextTick can starve the event loop if used incorrectly.

Example:

```js
process.nextTick(() => console.log("tick"));
Promise.resolve().then(() => console.log("promise"));
setTimeout(() => console.log("timer"), 0);
```

Output:

```
tick
promise
timer
```

## 13. Putting It All Together With a Simple Example

Consider:

```js
setTimeout(() => console.log("timeout"), 0);
setImmediate(() => console.log("immediate"));
Promise.resolve().then(() => console.log("promise"));
console.log("sync");
```

Let’s go step by step.

1. sync runs first\
   prints: sync
2. promise callback goes to microtask queue
3. setTimeout callback goes to timers queue
4. setImmediate callback goes to check queue
5. event loop finishes sync code
6. run all microtasks\
   prints: promise
7. enter timers phase\
   timer is due\
   prints: timeout
8. enter pending → idle → prepare\
   nothing happens
9. enter poll phase\
   nothing to do
10. enter check phase\
    prints: immediate

Final output:

```
sync
promise
timeout
immediate
```

## 14. Why setImmediate Sometimes Runs Before setTimeout 0

Because:

* setTimeout waits for the next “timers phase”
* setImmediate waits for the “check phase”
* depending on whether poll has I/O waiting, poll might move straight to check

This is why the order is not guaranteed:

Sometimes:

```
immediate
timeout
```

Sometimes:

```
timeout
immediate
```

But Promise microtasks will always run before both.

## 15. I/O Changes the Order

If there is I/O:

```js
const fs = require("fs");

fs.readFile(__filename, () => {
  setTimeout(() => console.log("timeout"), 0);
  setImmediate(() => console.log("immediate"));
});
```

Guaranteed output:

```
immediate
timeout
```

Why?

Because after I/O, the event loop jumps directly to the check phase.

This is a very common interview question.

## 16. A Visual Timeline To Remember

This is the natural order in most normal cases:

1. Timers
2. Pending callbacks
3. Idle / prepare
4. Poll
5. Microtasks
6. Check (setImmediate)
7. Close callbacks

Microtasks run after EVERY callback.

nextTick runs before microtasks.

## 17. The Simplified Model (The One You Should Remember)

To avoid overwhelming yourself, memorize only this:

synchronous code runs first\
then nextTick\
then promise microtasks\
then timers\
then I/O callbacks\
then setImmediate\
then close callbacks

Everything else is details.

## 18. Summary

You now understand:

why the event loop exists\
how the event loop phases work\
the role of timers, pending callbacks, poll, check, close\
how setTimeout, setImmediate, and I/O interact\
why microtasks run before macrotasks\
why nextTick runs before microtasks\
why the order of async operations changes depending on I/O\
how the event loop schedules callbacks step by step\
how Node processes each type of event

This chapter completes your understanding of the event loop at the libuv level.
