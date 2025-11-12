# What is Node.js?

#### About Node.js <a href="#about-node.js" id="about-node.js"></a>

Node.js, created in 2009 by Ryan Dahl, is an open-source, cross-platform runtime environment for executing JavaScript code outside a web browser. Primarily used for server-side programming, Node.js allows JavaScript to be used for both client- and server-side applications, utilizing an event-driven architecture that efficiently handles numerous concurrent connections via a single thread. It incorporates the V8 JavaScript engine and is supported by the Node.js Foundation. Node.js is often used for real-time applications.

#### 1. Why Node.js Exists <a href="#id-1.-why-node.js-exists" id="id-1.-why-node.js-exists"></a>

*   Originally, **JavaScript could only run inside browsers** (like Chrome, Firefox, or Edge).

    It was used mainly to make web pages interactive — for example, to show popups or handle button clicks.
* But developers wanted to use JavaScript **outside the browser**, for things like:
  * Creating web servers
  * Accessing files on a computer
  * Building backend APIs
* That’s where **Node.js** comes in — it made JavaScript work on computers and servers, not just in browsers.

#### 2. What Node.js Actually Is <a href="#id-2.-what-node.js-actually-is" id="id-2.-what-node.js-actually-is"></a>

*   **Node.js is a JavaScript runtime environment.**

    That means it’s a program that can **read and execute JavaScript code** — just like browsers do.
* **But unlike browsers**, Node.js runs on your **computer or server**, not inside a webpage.

#### 3. What Node.js Is Made Of <a href="#id-3.-what-node.js-is-made-of" id="id-3.-what-node.js-is-made-of"></a>

*   **Node.js itself is a C++ program.**

    It was written in C++ so it can communicate directly with your **computer’s operating system**, read files, handle networks, etc.
* Inside this C++ program, Node.js includes **Google’s V8 JavaScript engine** — the same engine that Chrome uses to run JavaScript.
*   You can think of it like this:

    Copy

    ```
    Node.js (C++ program)
      ├── V8 Engine → Executes your JavaScript code
      ├── C++ Bindings → Give JavaScript access to files, network, etc.
      └── Node.js APIs → Prebuilt modules (like fs, http, path, os)
    ```

#### 4. How Node.js Works Behind the Scenes <a href="#id-4.-how-node.js-works-behind-the-scenes" id="id-4.-how-node.js-works-behind-the-scenes"></a>

1. You write JavaScript code in a file (for example, `app.js`).
2. You run it using the `node` command in your terminal.
3. Node.js (the C++ program) loads your file.
4. The **V8 engine** inside Node.js **compiles** your JavaScript code into machine code.
5. The C++ part of Node.js adds extra powers (through APIs) so JavaScript can do things like read files or start a web server.

#### 5. What Makes Node.js Special <a href="#id-5.-what-makes-node.js-special" id="id-5.-what-makes-node.js-special"></a>

* It gives JavaScript **new capabilities** that browsers don’t allow, such as:
  * Accessing your file system (**`fs`** module)
  * Creating web servers (**`http`** module)
  * Working with the operating system (**`os`** module)
* **Browser objects like `window` or `document` do not exist in Node.js**, because those belong to the browser environment, not Node.js.
* Node.js uses a **single-threaded, event-driven, non-blocking I/O model**, which means it can handle lots of tasks at once efficiently — perfect for real-time or high-traffic applications.
