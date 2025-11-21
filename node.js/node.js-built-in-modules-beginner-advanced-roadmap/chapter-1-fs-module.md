---
description: >-
  The Ultimate Beginner-Friendly Guide to Reading, Writing, and Managing Files
  in Node.js
---

# 📘 CHAPTER 1 — FS Module

## 1. Introduction

Imagine Node.js is a robot. It can read, write, and modify files on your computer just like you can — but faster, safer, and much more consistently. The part of Node that gives it these superpowers is the **FS module**, which stands for **File System**.

If you’re building a Node.js backend, CLI tool, automation script, or even a small utility, you **will** use the FS module sooner or later. It’s one of the most important built-in modules in Node.js.

In this chapter, you will learn:

* how to read files
* how to write files
* how to update files
* how to create folders
* how to delete items
* how to check if something exists
* when to use sync vs async versions
* how file streaming works (like reading big files without crashing)
* real project examples

Let’s begin your first real adventure inside the heart of Node.js.



## 2. The FS Module in One Sentence

> The FS module allows your Node.js application to interact with files and directories on your computer.

You load it using:

```js
const fs = require("fs");
```

or ES module:

```js
import fs from "fs";
```

Node provides **both asynchronous and synchronous** versions of almost every FS function.

Example:

* `fs.readFile()` → async
* `fs.readFileSync()` → sync

We will learn when to use which.



## 3. Reading Files (Your First File Operation)

Reading a file is like opening a notebook.

### Async version (recommended for most apps)

```js
fs.readFile("info.txt", "utf8", (err, data) => {
  if (err) {
    console.error("Error reading file:", err);
    return;
  }
  console.log(data);
});
```

#### Why `"utf8"`?

Without it, Node will give you raw binary data (Buffer).

#### When to use async?

* servers
* production apps
* anything that handles many requests

Async means:\
**your app won’t freeze while waiting for the file.**

### Sync version (simple scripts)

```js
const data = fs.readFileSync("info.txt", "utf8");
console.log(data);
```

#### When to use sync?

* one-time scripts
* CLI tools
* startup code
* unit tests

Sync stops the entire program until the file is read — useful sometimes, dangerous on servers.



## 4. Writing Files (Creating or Replacing)

Writing is like creating a new file or replacing its content.

### Async write

```js
fs.writeFile("note.txt", "Hello, world!", (err) => {
  if (err) throw err;
  console.log("File created!");
});
```

If the file exists, **it will be replaced**.

### Sync write

```js
fs.writeFileSync("note.txt", "Hello, world!");
```



## 5. Appending Files (Adding Content)

If you want to add something to an existing file instead of overwriting:

### Async append

```js
fs.appendFile("log.txt", "New log entry\n", (err) => {
  if (err) throw err;
  console.log("Logged!");
});
```

### Sync append

```js
fs.appendFileSync("log.txt", "New log entry\n");
```

Useful for:

* logging
* activity history
* saving user actions



## 6. Checking If a File Exists

### Using `fs.existsSync()` (simple)

```js
if (fs.existsSync("config.json")) {
  console.log("Config found!");
}
```

### Using async version (recommended)

```js
fs.access("config.json", (err) => {
  if (err) {
    console.log("File does not exist.");
  } else {
    console.log("File exists!");
  }
});
```



## 7. Deleting Files

### Async delete

```js
fs.unlink("old.txt", (err) => {
  if (err) throw err;
  console.log("Deleted!");
});
```

### Sync delete

```js
fs.unlinkSync("old.txt");
```

Useful for temporary files, cleanup tasks, etc.



## 8. Creating Directories

### Async mkdir

```js
fs.mkdir("photos", (err) => {
  if (err) throw err;
  console.log("Folder created!");
});
```

### Recursively create nested folders

```js
fs.mkdir("uploads/images/2025", { recursive: true }, (err) => {
  if (err) throw err;
  console.log("Nested folders created!");
});
```



## 9. Reading Directories

List all files inside a folder:

```js
fs.readdir("uploads", (err, files) => {
  if (err) throw err;
  console.log(files);
});
```

Output:

```
['photo1.jpg', 'photo2.png', 'notes.txt']
```



## 10. Deleting Directories

```js
fs.rmdir("old_folder", (err) => {
  if (err) throw err;
  console.log("Folder removed!");
});
```

Or recursively:

```js
fs.rm("old_folder", { recursive: true, force: true }, (err) => {
  if (err) throw err;
  console.log("Folder removed!");
});
```



## 11. Renaming Files or Folders

```js
fs.rename("old.txt", "new.txt", (err) => {
  if (err) throw err;
  console.log("Renamed!");
});
```

Works for both files and folders.



## 12. Watching Files (Like Nodemon)

Node can watch files for changes:

```js
fs.watch("notes.txt", () => {
  console.log("notes.txt was changed");
});
```

Useful for:

* automatic reload
* monitoring config changes
* building tools like nodemon



## 13. Working with JSON Files

Reading JSON:

```js
const data = JSON.parse(fs.readFileSync("data.json", "utf8"));
console.log(data);
```

Writing JSON:

```js
fs.writeFileSync("data.json", JSON.stringify({ user: "Saabbir" }, null, 2));
```

Note the pretty-print spacing (`2`).



## 14. Buffers (Binary Data)

If you read a file without encoding:

```js
fs.readFile("image.png", (err, buffer) => {
  console.log(buffer);
});
```

You get a **Buffer**, which is raw binary data.

Useful for:

* images
* videos
* audio
* ZIP files
* streaming



## 15. File Streams (Reading Big Files Without Crashing)

Reading a 2GB file using `readFile()` will crash your app.

Instead, use streams:

```js
const stream = fs.createReadStream("bigfile.mp4");

stream.on("data", (chunk) => {
  console.log("Received chunk:", chunk.length);
});

stream.on("end", () => {
  console.log("File finished!");
});
```

This reads the file **piece by piece**, like watching a movie buffer.



## 16. Stream Piping (Copying Big Files)

Copy a file efficiently:

```js
fs.createReadStream("movie.mp4")
  .pipe(fs.createWriteStream("copy.mp4"));
```

This is incredibly fast and efficient.



## 17. Real Project Examples

#### Example 1: Create a simple logger

```js
function log(message) {
  const line = `[${new Date().toISOString()}] ${message}\n`;
  fs.appendFile("log.txt", line, (err) => {
    if (err) throw err;
  });
}

log("User logged in");
log("Payment processed");
```

#### Example 2: Reading configuration safely

```js
let config = {};

if (fs.existsSync("config.json")) {
  config = JSON.parse(fs.readFileSync("config.json", "utf8"));
} else {
  config = { mode: "development" };
}
```

#### Example 3: Creating a folder if missing

```js
if (!fs.existsSync("uploads")) {
  fs.mkdirSync("uploads");
}
```

#### Example 4: Simple file-based database

```js
function readDB() {
  if (!fs.existsSync("db.json")) return [];
  return JSON.parse(fs.readFileSync("db.json", "utf8"));
}

function writeDB(data) {
  fs.writeFileSync("db.json", JSON.stringify(data, null, 2));
}

const db = readDB();
db.push({ id: Date.now(), name: "New User" });
writeDB(db);
```

## 18. Sync vs Async: When to Use Which

| Use Case                    | Async | Sync |
| --------------------------- | ----- | ---- |
| Web servers                 | ✅ yes | ❌ no |
| CLI tools                   | ok    | ok   |
| Scripts                     | ok    | ok   |
| Large apps                  | yes   | no   |
| One-time startup tasks      | ok    | ok   |
| File manipulation pipelines | yes   | no   |

Golden rule:

> If your app handles many users or requests, always use async.



## 19. Node’s Error-First Callback Pattern

Almost all asynchronous FS functions use the **error-first callback pattern**.

This means the callback looks like:

```js
function callback(err, data) {}
```

* `err` → always the first argument
* `data` → the successful result

Why error-first?

Because Node.js runs asynchronously and errors might happen at any time.\
The rule is:

* If something goes wrong → `err` is an Error object
* If everything works → `err` is null

Example:

```js
fs.readFile("file.txt", "utf8", (err, data) => {
  if (err) {
    console.log("Something went wrong:", err);
    return;
  }

  console.log("File contents:", data);
});
```

Important rule:

> Always check the `err` argument before using `data`.



## 20. Summary

In this chapter, you learned:

* how to read, write, append, delete, rename files
* how to create and remove directories
* how to check for file existence
* how to watch files
* how to work with JSON
* how buffers and binary data work
* how file streams work
* when to use sync vs async versions
* real-world use cases

The FS module is one of the most essential tools in Node.js, and now you understand it at a professional, practical level.
