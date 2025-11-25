---
description: >-
  How to build web servers, handle requests, send responses, work with routes,
  and understand how HTTP works inside Node.js
---

# 📘 CHAPTER 3 — HTTP Module

## 1. Introduction

The **http** module is one of the most important modules in Node.js.

Why?\
Because Node.js was originally built to run servers — especially fast, lightweight, event-driven servers.

When you install Node.js, you already have everything you need to build:

simple websites\
REST APIs\
JSON endpoints\
file download responses\
streaming servers\
reverse proxies\
real-time systems\
microservices

And all of this starts with a single built-in module:

`http`

In this chapter, you’ll learn exactly how to use it — without frameworks, without Express, without magic.\
Just pure, foundational Node.js.

## 2. What the HTTP Module Does

The http module lets you:

create web servers\
receive incoming requests\
send responses\
access headers\
handle methods (GET, POST, etc.)\
stream data to clients\
work with JSON APIs\
set status codes\
build routing manually

Every framework (Express, Fastify, Koa, NestJS) is built on top of the `http` module.\
So understanding http gives you total control.

## 3. Creating Your First HTTP Server

Let’s start simple.

```js
const http = require("http");

const server = http.createServer((req, res) => {
  res.end("Hello World");
});

server.listen(3000);
console.log("Server running on http://localhost:3000");
```

When you visit:

```
http://localhost:3000
```

You’ll see:

```
Hello World
```

Congratulations — that’s a real web server.

## 4. Understanding req and res

The callback:

```js
(req, res) => { ... }
```

receives two objects:

#### req — Incoming request

Contains:

method (GET, POST, etc.)\
url (/products, /api/users)\
headers\
query strings\
body (stream)

Example:

```js
console.log(req.method); // "GET"
console.log(req.url);    // "/"
console.log(req.headers);
```

#### res — Outgoing response

Allows you to:

write headers\
send body\
set status code\
close response

Example:

```js
res.statusCode = 200;
res.setHeader("Content-Type", "text/plain");
res.end("OK");
```

## 5. Setting Status Codes

```js
res.statusCode = 404;
res.end("Not Found");
```

Other examples:

200 – OK\
201 – Created\
301 – Redirect\
400 – Bad Request\
401 – Unauthorized\
500 – Server Error

Example:

```js
res.writeHead(201, { "Content-Type": "application/json" });
res.end(JSON.stringify({ message: "Created" }));
```

## 6. Sending JSON

Very common for APIs:

```js
res.setHeader("Content-Type", "application/json");
res.end(JSON.stringify({ name: "Saabbir" }));
```

Or shorter:

```js
res.writeHead(200, { "Content-Type": "application/json" });
res.end(JSON.stringify({ success: true }));
```

## 7. Basic Routing (Without a Framework)

Routing simply means:\
“Do something different based on req.url and req.method.”

Example:

```js
const server = http.createServer((req, res) => {
  if (req.url === "/" && req.method === "GET") {
    res.end("Home Page");
  }
  else if (req.url === "/about") {
    res.end("About Page");
  }
  else {
    res.statusCode = 404;
    res.end("Not Found");
  }
});
```

This is how Express internally works — it just makes routing easier.

## 8. Handling Query Strings

Request example:

```
/search?query=node&limit=10
```

Extract using WHATWG URL:

```js
const url = new URL(req.url, `http://${req.headers.host}`);
console.log(url.searchParams.get("query"));
console.log(url.searchParams.get("limit"));
```

searchParams is extremely useful.

## 9. Handling POST Request Bodies

Important:\
req is a **readable stream**.

You must collect the body manually:

```js
let body = "";

req.on("data", chunk => {
  body += chunk.toString();
});

req.on("end", () => {
  console.log("Body:", body);
  res.end("Received");
});
```

For JSON:

```js
const data = JSON.parse(body);
```

Later, frameworks (like Express) automate this for you.\
But knowing the manual way is important.

## 10. Sending Files (Without Express)

Use fs streams:

```js
const fs = require("fs");

if (req.url === "/video") {
  const stream = fs.createReadStream("movie.mp4");
  res.writeHead(200, { "Content-Type": "video/mp4" });
  stream.pipe(res);
}
```

This uses **zero buffering** and **backpressure handling** — the best method.

## 11. Streaming Responses

You can write pieces of data over time:

```js
res.write("Hello ");
setTimeout(() => res.write("World "), 1000);
setTimeout(() => res.end("!"), 2000);
```

Your browser receives chunks as they are written.

This is how:

live logs\
Server-Sent Events\
chunked HTML\
long polling\
chat systems

work internally.

## 12. Redirects

```js
res.writeHead(302, { Location: "/login" });
res.end();
```

Or permanent:

```js
res.writeHead(301, { Location: "https://google.com" });
res.end();
```

## 13. Working With Headers

Reading headers:

```js
console.log(req.headers["user-agent"]);
```

Sending headers:

```js
res.setHeader("Content-Type", "text/html");
```

Sending multiple with writeHead:

```js
res.writeHead(200, {
  "Content-Type": "text/html",
  "X-Powered-By": "Node.js"
});
```

## 14. Understanding Keep-Alive

Node uses persistent TCP connections by default:

The same connection can handle multiple requests.\
This makes servers faster and reduces overhead.

You can control it:

```js
res.setHeader("Connection", "keep-alive");
```

or disable it:

```js
res.setHeader("Connection", "close");
```

## 15. Handling Errors Safely

Wrap body parsing, JSON parsing, or file reading in try/catch inside event callbacks:

```js
req.on("end", () => {
  try {
    const data = JSON.parse(body);
    res.end("OK");
  } catch (err) {
    res.statusCode = 400;
    res.end("Invalid JSON");
  }
});
```

## 16. Building a Simple API

Example: `/api/time`

```js
if (req.url === "/api/time") {
  res.writeHead(200, { "Content-Type": "application/json" });
  res.end(JSON.stringify({ time: Date.now() }));
}
```

Example: `/api/echo`

```js
if (req.url === "/api/echo" && req.method === "POST") {
  let body = "";
  req.on("data", chunk => body += chunk);
  req.on("end", () => {
    res.writeHead(200, { "Content-Type": "application/json" });
    res.end(body);
  });
}
```

## 17. Using Environment Variables for Port

```js
const PORT = process.env.PORT || 3000;
server.listen(PORT);
```

This is standard practice for deployment.

## 18. Summary

You now understand how to use Node’s `http` module:

creating HTTP servers\
handling requests & responses\
sending JSON\
creating custom routing\
handling GET/POST\
parsing query strings\
working with headers\
using streams for files\
implementing redirects\
writing streaming responses\
managing keep-alive\
processing request bodies manually\
returning status codes\
building real APIs

The http module is the foundation of backend development in Node.js.\
Every framework builds on this knowledge.
