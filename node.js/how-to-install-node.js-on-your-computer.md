# How to Install Node.js on Your Computer

When you’re just getting started with JavaScript beyond the browser, one of the first tools you’ll encounter is **Node.js**. It allows you to run JavaScript on your computer — not just inside web pages and opens the door to building servers, APIs, command-line tools, and much more.

In this guide, we’ll walk through installing Node.js in a beginner-friendly way, explaining every step so you not only _do it_, but also _understand_ what’s happening.



## 🧠 What Is Node.js?

At its core, **Node.js** is a runtime environment built on Chrome’s V8 JavaScript engine.\
In simple terms, it lets you run JavaScript directly from your terminal — outside of a web browser.

When you install Node.js, you also get **npm** (Node Package Manager), which helps you install and manage third-party libraries that make development faster and easier.



## 🖥️ Step 1: Installing Node.js (The Easy Way)

The simplest way to install Node.js is by downloading the official installer:

1. Go to [nodejs.org](https://nodejs.org)
2. You’ll see two versions:
   * **LTS (Long Term Support):** Stable and recommended for most users.
   * **Current:** The latest version with new features (may be less stable).
3. Download the installer for your operating system (Windows, macOS, or Linux).
4. Run the installer and follow the on-screen steps.

Once the installation finishes, open your terminal (Command Prompt, PowerShell, or Terminal) and check if Node.js and npm were installed correctly:

```bash
node -v
npm -v
```

You should see version numbers for both.



## 🧩 Step 2: Installing Node.js Using NVM (Recommended for Developers)

If you plan to work on multiple projects, you’ll quickly discover that **different projects require different Node.js versions**. That’s where **NVM** (Node Version Manager) comes in.

It lets you install, switch, and manage multiple Node.js versions effortlessly.

### 🔧 Installing NVM

* For **macOS/Linux**, visit the official repository:\
  👉 [https://github.com/nvm-sh/nvm](https://github.com/nvm-sh/nvm)
* For **Windows**, use the Windows version of NVM:\
  👉 [https://github.com/coreybutler/nvm-windows](https://github.com/coreybutler/nvm-windows)

Follow the installation steps for your OS, and restart your terminal when it’s done.



## ⚙️ Step 3: Managing Node.js Versions with NVM

Once NVM is installed, you can use it to install or switch between Node.js versions.

#### 📦 Install a specific version:

```bash
nvm install 18
```

#### 🔁 Use a specific version:

```bash
nvm use 18
```

#### 📋 List all installed versions:

```bash
nvm ls
```

#### 🧹 Remove a specific version:

```bash
nvm uninstall 16
```

#### 🌐 List all available versions:

```bash
nvm ls-remote
```

💡 **Tip:**

* LTS versions usually have **even numbers** (e.g., 18, 20).
* Current versions usually have **odd numbers** (e.g., 19, 21).

#### 🔍 Check which version you’re currently using:

```bash
nvm current
```



## 📦 Step 4: Using NPM (Node Package Manager)

Once Node.js is installed, you automatically get **npm**, the tool developers use to install JavaScript libraries (called “packages”).

Here are the most common npm commands you’ll use:

### 🧰 Basic Commands

| Action                  | Command                   |
| ----------------------- | ------------------------- |
| Check npm version       | `npm -v`                  |
| Install a package       | `npm install <package>`   |
| Uninstall a package     | `npm uninstall <package>` |
| Update a package        | `npm update <package>`    |
| List installed packages | `npm list`                |
| Search for a package    | `npm search <package>`    |

### 🌍 Global Packages

Sometimes you’ll want to install a tool globally so it’s available everywhere on your system:

| Action               | Command                      |
| -------------------- | ---------------------------- |
| Install globally     | `npm install -g <package>`   |
| Uninstall globally   | `npm uninstall -g <package>` |
| Update globally      | `npm update -g <package>`    |
| List global packages | `npm list -g`                |



## 💡 Step 5: Trying Out Node.js (Your First Command)

Once everything’s installed, open your terminal and type:

```bash
node
```

You’ll enter the **Node REPL (Read-Eval-Print Loop)** — a live JavaScript playground where you can type and run code instantly.

Example:

```bash
> console.log("Hello, Node.js!");
Hello, Node.js!
```

To exit the REPL, type:

```bash
.exit
```

or press **Ctrl + C** twice.



## 🚀 Step 6: Running JavaScript Files

Let’s say you have a file named `app.js` with this code:

```js
console.log("My first Node.js program!");
```

To run it, open your terminal in the same directory and type:

```bash
node app.js
```

That’s it! You’ve just executed a JavaScript file using Node.js.



## 🧭 Summary

Here’s what you’ve learned:

✅ What Node.js is and why it’s useful\
✅ How to install Node.js using the official installer or NVM\
✅ How to manage multiple Node.js versions with NVM\
✅ How to use npm to install and manage packages\
✅ How to test Node.js using the REPL or a JavaScript file

You’re now ready to start exploring Node.js and building your first real applications!



## 🪄 Bonus Tip

Whenever you’re unsure, you can always check your current versions:

```bash
node -v
npm -v
```

Keeping your Node.js and npm versions up to date will ensure you always have the latest security fixes and features.
