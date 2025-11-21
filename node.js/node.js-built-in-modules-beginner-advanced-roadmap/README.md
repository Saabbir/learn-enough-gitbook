---
description: A structured curriculum for learning built-in modules in the right order.
---

# 📘 Node.js Built-In Modules — Beginner → Advanced Roadmap

## ⭐ Beginner Modules

These are the **core** modules every Node developer uses.\
They help you build basic servers, work with files, handle paths, and understand Node.js fundamentals.

#### **1. FS (File System) — Core Beginner Chapter**

You’ll learn:

* reading & writing files
* directories
* async/sync methods
* streams basics
* JSON work
* file watchers

#### **2. PATH — Essential for file operations**

You’ll learn:

* path.join
* path.resolve
* \_\_dirname issues
* parsing & formatting
* dealing with Windows vs Linux paths

#### **3. HTTP — Foundation of Node servers**

You’ll learn:

* creating HTTP servers
* request/response flow
* streaming responses
* simple routing
* basic REST API

#### **4. EVENTS — Event-driven programming**

* EventEmitter
* building your own events
* internal Node.js events
* relationship with the event loop

#### **5. OS — System information**

* CPU count (useful for clustering)
* memory info
* platform detection
* working with temp directories

#### **6. PROCESS — Everything about your running app**

* environment variables
* reading arguments
* exit codes
* stdout / stderr
* process events

➡️ **These beginner modules form the foundation of real Node.js development.**



## ⭐ Intermediate Modules

These are **practical real-world modules** used often in backend development, CLI apps, and optimization.

#### **7. TIMERS**

* setTimeout
* setInterval
* setImmediate
* nextTick
* where they fit in the event loop

#### **8. URL**

* URL parsing
* query parameters
* working with URLSearchParams

#### **9. QUERYSTRING (legacy but still used)**

* old-school URL parsing
* form handling
* when not to use it

#### **10. UTIL**

* util.promisify
* util.format
* util.debuglog
* inheritance helpers

#### **11. READLINE**

* building CLI applications
* reading user input
* streaming line by line

#### **12. ZLIB**

* compression
* gzip files
* streaming compression

#### **13. NET**

* TCP servers
* raw socket programming
* low-level networking

➡️ These are modules you use when writing **real software: CLIs, servers, tools, pipelines, dashboards, etc.**



## ⭐ Advanced Modules

These are for high-performance apps, multithreading, child processes, scaling, or deep Node internals.

#### **14. STREAMS (Advanced)**

* readable
* writable
* transform streams
* backpressure
* building high-performance streaming apps

#### **15. CRYPTO**

* hashing
* random bytes
* encryption/decryption
* HMAC
* secure password storage

#### **16. CHILD\_PROCESS**

* exec
* spawn
* fork
* building real CLI tools
* running shell commands

#### **17. WORKER\_THREADS**

* parallel processing
* CPU-bound tasks
* thread pools

#### **18. CLUSTER**

* multi-process scaling
* load balancing
* multi-core utilization

#### **19. DNS**

* DNS lookups
* network-level operations

#### **20. ASSERT**

* writing simple tests
* assertion-based testing tools

➡️ These will be covered later once you complete beginner + intermediate modules.
