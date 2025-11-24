---
description: >-
  How Node.js handles HTTP, TCP, sockets, and network communication without ever
  blocking the event loop.
---

# 📘 CHAPTER 8 — Networking Internals

## 1. Introduction

Node.js was originally created to build fast, scalable, real-time network applications.\
This is why the **networking layer** is one of the strongest parts of Node.

To understand Node’s true power, you must know:

how Node handles incoming network requests\
how it sends responses\
how it manages TCP connections\
how it interacts with the operating system\
how it deals with many connections at once\
why Node is so fast at I/O\
why it can handle thousands of clients at the same time\
This chapter will answer all of these questions in a very simple and beginner-friendly way.

## 2. Networking Starts With the Operating System (Not Node)

Node.js does not directly talk to the internet.\
It relies on your operating system, because the OS has the low-level networking features:

Linux → epoll\
macOS → kqueue\
BSD → kevent\
Windows → IOCP

These systems notify Node when:

* data is ready to read
* a new connection arrives
* a connection closes
* errors happen
* a socket is writable

Node does not “check” for network events.\
It waits for the OS to notify it.

This notification system is extremely fast and efficient.

## 3. TCP vs HTTP: Node Handles Both

Node.js uses:

net module → low-level TCP\
http module → high-level HTTP built on TCP

Everything in HTTP travels over TCP.

TCP provides:

connection establishment (3-way handshake)\
ensures packets arrive in order\
retransmission of lost packets\
stream-based data transfer\
error detection\
Node.js simply listens for TCP events from the OS.

## 4. The Flow of an Incoming Request

Let’s walk through what happens when a user accesses your server:

User types your website URL\
Browser sends an HTTP request\
OS network stack receives packets\
OS notifies libuv that a socket has data\
libuv wakes up the event loop\
Node’s TCP handler reads data from the socket\
Node parses the raw bytes into an HTTP request object\
You receive req and res objects in your code

This is all event-driven and non-blocking.

The OS does the heavy work.\
libuv waits.\
Node simply responds when something is ready.

## 5. Why Node Uses Event-Driven Networking

Most older servers use a thread-per-connection model:

Connection 1 → Thread 1\
Connection 2 → Thread 2\
Connection 3 → Thread 3

This doesn’t scale well:

threads use memory\
threads use CPU\
context switching becomes expensive\
too many connections overwhelm the system

Node uses a completely different model:

All connections share one JavaScript thread\
Connections generate events\
Node reacts to those events

This allows Node to handle **thousands** of connections with minimal overhead.

## 6. Example: A Simple TCP Server

This server echoes back whatever the client sends:

```js
const net = require("net");

const server = net.createServer(socket => {
  console.log("Client connected");

  socket.on("data", data => {
    console.log("Received:", data.toString());
    socket.write("You said: " + data);
  });

  socket.on("end", () => {
    console.log("Client disconnected");
  });
});

server.listen(3000, () => {
  console.log("TCP server running on port 3000");
});
```

What’s happening internally?

OS accepts the TCP connection\
OS notifies libuv: "New socket available"\
libuv wakes the event loop\
Node creates a socket object\
Your callback gets that socket

Everything is event-driven.

## 7. Example: An HTTP Server

```js
const http = require("http");

const server = http.createServer((req, res) => {
  res.write("Hello world");
  res.end();
});

server.listen(3000);
```

Internally:

HTTP server is actually a wrapper around a TCP server\
Node parses raw TCP data into structured HTTP objects\
req is a readable stream\
res is a writable stream

Node handles:

headers\
body\
request method\
URL parsing\
chunked transfer encoding

All this work happens efficiently because Node is streaming-based.

## 8. Why req and res Are Streams

req (incoming data) → readable stream\
res (outgoing data) → writable stream

Streams allow Node to:

receive data gradually\
send data gradually\
handle large files without loading into memory\
work with chunked encoding\
achieve backpressure automatically\
This is why serving files using streams is a best practice.

Example:

```js
const fs = require("fs");
const http = require("http");

http.createServer((req, res) => {
  fs.createReadStream("bigfile.mp4").pipe(res);
});
```

Zero memory pressure, highly scalable.

## 9. What Happens When a Server Handles 10,000 Connections?

Node does not create 10,000 threads.\
The OS handles all connections.\
The OS notifies Node when each socket has new data.\
Node reacts by running callbacks.

The event loop never blocks because:

TCP events are non-blocking\
data arrives asynchronously\
callbacks run very quickly\
This is why Node is perfect for:

real-time apps\
chat apps\
live dashboards\
IoT\
APIs with lots of traffic\
backend proxy services

## 10. Keep-Alive and Connection Reuse

HTTP/1.1 defaults to keep-alive.

Meaning:

the connection is not closed after one request\
multiple requests reuse the same socket\
This saves a lot of overhead.

Node supports keep-alive automatically.

Example:

browser requests:

* HTML
* CSS
* JS
* images

Reusing the same TCP connection makes everything much faster.

## 11. How Node Reads Data from a Socket

Sockets don’t deliver data all at once.\
They arrive in chunks.

Node buffers these chunks and triggers “data” events:

```js
socket.on("data", chunk => {
  console.log(chunk.length);
});
```

Node reads only when the OS says “new chunk is ready.”

This means:

Node doesn’t waste CPU checking the socket\
Node doesn’t poll aggressively\
Node only reacts when events happen

## 12. Backpressure in Networking

Just like file streams, network streams can be too slow or too fast.

If you write too fast to a socket:

```js
const ok = socket.write(hugeChunk);
```

ok becomes false when internal buffers are full.

Then you wait for:

```js
socket.on("drain", () => {
  // continue writing
});
```

This prevents flooding the connection.

Node applies backpressure automatically when you use streams and pipe().

## 13. HTTPS Internals

HTTPS adds a TLS layer on top of TCP.

TLS handshake is handled partly by:

OpenSSL (native C)\
Node.js internal bindings\
Thread pool (for some crypto operations)

Once the connection is established:

All encrypted/decrypted data still flows as normal TCP chunks\
req and res still behave as streams\
Node still handles everything event-driven

## 14. Connection Life Cycle in Node

A connection goes through these stages:

TCP connection established\
optional TLS handshake\
request arrives\
Node parses HTTP\
req event emitted\
developer code handles request\
response sent\
connection kept alive or closed\
Node cleans up

Everything involves events delivered to the event loop.

## 15. Why Networking in Node.js Never Blocks

No part of TCP or HTTP parsing uses JavaScript CPU-expensive loops.\
The OS handles network events entirely in the background.\
Node simply listens for events and schedules callbacks.\
The event loop only runs short JavaScript functions.\
No waiting is done inside JavaScript for networks.

This is why Node servers rarely block or freeze from I/O.

Blocking only happens when developers accidentally do CPU-heavy JavaScript work.

## 16. Summary

You now understand:

how Node interacts with the operating system\
how TCP connections are created and managed\
how HTTP sits on top of TCP\
how req and res work as streams\
why Node can handle thousands of connections\
how backpressure works in networking\
how Node avoids blocking during network operations\
how keep-alive improves performance\
how sockets deliver data in chunks\
how the event loop wakes up for network events

This chapter gives you a clear picture of why Node is such a powerful networking platform.
