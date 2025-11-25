---
description: >-
  How Node.js handles large data efficiently using readable, writable,
  transform, and duplex streams — including backpressure, piping, chunking, and
  real-world examples
---

# 📘 CHAPTER 15 — Streams Module

## 1. Introduction

Imagine you’re trying to read a **10GB file** with:

```js
fs.readFile("bigfile.txt", (err, data) => {});
```

Node will attempt to load all 10GB into memory at once.\
Most systems will crash or freeze instantly.

This is where **Streams** become one of Node.js’s most powerful features.

Streams allow you to process data **piece by piece**, in small chunks, without loading everything at once. This makes them ideal for:

reading large files\
writing large files\
network communication\
HTTP request/response bodies\
video/audio streaming\
compression (gzip)\
encryption/decryption\
proxying data\
real-time pipelines

Streams are everywhere in Node.js — so understanding them unlocks a huge part of the platform.

## 2. What Are Streams?

A stream is simply a way to work with data **gradually** instead of all at once.

Data flows in **chunks**, like water flowing through a pipe:

chunk → chunk → chunk → done

This allows Node to:

use low memory\
stay fast\
avoid blocking the event loop\
respond to I/O efficiently

Streams are built on **EventEmitter**, so they use events like:

data\
end\
error\
close\
drain\
readable\
finish

## 3. Types of Streams

Node has four main types:

1. **Readable Streams**\
   You read data from them\
   Examples: fs.createReadStream, http.IncomingMessage, process.stdin
2. **Writable Streams**\
   You write data to them\
   Examples: fs.createWriteStream, http.ServerResponse, process.stdout
3. **Duplex Streams**\
   Both readable + writable\
   Example: net.Socket
4. **Transform Streams**\
   Duplex streams that **modify** data\
   Examples: zlib gzip, crypto cipher, custom transformers

Understanding these four types is the foundation.

## 4. Readable Stream Example: Reading a Big File

```js
const fs = require("fs");

const stream = fs.createReadStream("big.txt", { encoding: "utf8" });

stream.on("data", chunk => {
  console.log("Received chunk:", chunk.length);
});

stream.on("end", () => {
  console.log("Done reading");
});
```

Here’s what happens:

Node reads the file chunk by chunk\
Each chunk triggers the "data" event\
The stream ends when no more data remains\
The memory footprint stays tiny

This is infinitely more efficient than fs.readFile for large files.

## 5. Writable Stream Example: Writing a Large File

```js
const fs = require("fs");
const stream = fs.createWriteStream("output.txt");

stream.write("Hello\n");
stream.write("More data\n");
stream.end("Finish\n");
```

stream.end() signals that no more data will be written.

Writable streams buffer data automatically and flush when possible.

## 6. Why Streams Use Buffers

Streams don’t work with strings directly.\
They work with **Buffers**:

```js
stream.on("data", chunk => {
  console.log(Buffer.isBuffer(chunk)); // true
});
```

Buffers store raw binary data efficiently.\
This is essential for network traffic, file I/O, and binary formats.

You can convert buffers to strings using `.toString()`.

## 7. Piping Streams

The most powerful stream feature:

```js
readable.pipe(writable);
```

Example: copying a file:

```js
fs.createReadStream("input.txt")
  .pipe(fs.createWriteStream("output.txt"));
```

pipe() handles:

chunk flow\
backpressure\
pausing/resuming

This is why pipe() is the recommended way to connect streams.

## 8. Backpressure — The Most Important Stream Concept

Backpressure happens when:

the readable stream is fast\
the writable stream is slow

If we don’t control this:

memory grows\
buffers overflow\
Node slows down

pipe() handles backpressure automatically.

But if writing manually:

```js
if (!stream.write(chunk)) {
  stream.once("drain", () => {
    console.log("Wrote more after drain");
  });
}
```

Writable streams return **false** when their internal buffer is full.

## 9. HTTP Streams (req & res)

In an HTTP server:

req is a **readable** stream\
res is a **writable** stream

Example: echo server:

```js
http.createServer((req, res) => {
  req.pipe(res);
});
```

Data flows directly from client to server response.

Example: serving a file:

```js
http.createServer((req, res) => {
  fs.createReadStream("video.mp4").pipe(res);
});
```

This is how high-performance servers (YouTube, Netflix) deliver large content.

## 10. Streams and Asynchronous Flow

You can manually control reading:

```js
stream.on("readable", () => {
  let chunk;
  while((chunk = stream.read()) !== null) {
    console.log("Chunk:", chunk.length);
  }
});
```

This gives full control over chunk reading — useful for protocol parsing.

## 11. Writing a Simple Transform Stream

Transform streams let you **modify** data in transit.

Example: uppercase transform:

```js
const { Transform } = require("stream");

const upper = new Transform({
  transform(chunk, encoding, callback) {
    callback(null, chunk.toString().toUpperCase());
  }
});
```

Use it:

```js
process.stdin.pipe(upper).pipe(process.stdout);
```

Type something → output becomes uppercase.

## 12. Duplex Streams

Duplex = readable + writable\
But they don't have to transform data.

Example: network socket:

```js
socket.write("Hello");
socket.on("data", chunk => console.log(chunk.toString()));
```

Duplex streams allow full two-way communication.

## 13. Handling Stream Errors

Always handle errors:

```js
stream.on("error", err => {
  console.error("Stream error:", err);
});
```

Common errors:

file not found\
permission denied\
network connection lost

Streams fail silently unless you attach an error handler.

## 14. Ending a Writable Stream

finish event = writable done sending data:

```js
writable.on("finish", () => {
  console.log("All data written");
});
```

close event = underlying resource closed:

```js
writable.on("close", () => {
  console.log("File closed");
});
```

## 15. Pausing and Resuming Streams

Readable streams can be paused:

```js
readable.pause();
```

And resumed:

```js
readable.resume();
```

This is how you manually implement backpressure.

## 16. Real-World Streams Examples

#### 1. File upload handler

Read upload in chunks instead of loading entire file.

#### 2. Video streaming

Serve MP4 chunks using HTTP range requests.

#### 3. Log processing

Process huge logs line-by-line.

#### 4. Network proxy

Pipe data from client → upstream server → back to client.

#### 5. Image processing

Stream image into Sharp or ImageMagick.

#### 6. Compression

Use zlib transform streams:

```js
fs.createReadStream("file.txt")
  .pipe(zlib.createGzip())
  .pipe(fs.createWriteStream("file.txt.gz"));
```

## 17. Common Mistakes Beginners Make

Using fs.readFile for large files instead of streams\
Expecting streams to emit strings (they emit Buffers)\
Forgetting to handle errors\
Not understanding backpressure\
Forgetting to call stream.end() on writable streams\
Using pipe incorrectly (misordered connections)\
Blocking the event loop while reading chunks

## 18. Summary

You now understand everything essential about Node.js streams:

what streams are\
why streams exist\
how readable, writable, duplex, and transform streams work\
how Node handles large data\
using Buffers with streams\
how pipe connects streams\
how backpressure prevents overload\
how streams power HTTP, file I/O, and network traffic\
how to create custom transform streams\
error handling, finish events, and chunk flow

Streams are one of the most advanced and powerful features in Node.\
With this chapter, you now understand them deeply — and can build high-performance, scalable systems.
