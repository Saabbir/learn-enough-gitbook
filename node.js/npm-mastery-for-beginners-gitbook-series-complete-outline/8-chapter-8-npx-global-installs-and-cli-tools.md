---
description: >-
  A complete guide to using CLI tools in the Node.js ecosystem safely and
  professionally.
---

# 8️⃣ CHAPTER 8 — npx, Global Installs, and CLI Tools

## 1. Introduction

The Node.js ecosystem is filled with tools you run directly from the command line:

* TypeScript compiler (`tsc`)
* ESLint (`eslint`)
* Create React App
* Vite
* Next.js CLI
* Express generators
* Tailwind CLI
* Testing tools

These tools are known as **CLI tools** (Command Line Interface tools).

There are **three major ways** to run a CLI tool:

1. **Global installation**
2. **Local installation**
3. **Using npx (recommended)**

Each method has advantages, drawbacks, and specific use cases.\
This chapter explains how they work, when to use them, and how to avoid common mistakes.

## 2. What Is a CLI Tool in the Node Ecosystem?

A CLI tool is any npm package that exposes a command.

For example, if a package has this in its `package.json`:

```json
"bin": {
  "eslint": "./bin/eslint.js"
}
```

Then after installation, you can run:

```
eslint .
```

Examples of CLI tools you use every day:

* `npm` (itself a CLI tool)
* `node`
* `npx`
* `tsc`
* `eslint`
* `vite`
* `nodemon`

CLI tools are simply Node.js programs that run from your terminal.

## 3. Installing CLI Tools Globally

A global install makes the tool available system-wide:

```
npm install -g nodemon
```

After this, you can run:

```
nodemon server.js
```

Global installs place executables in:

* macOS/Linux: `/usr/local/bin/`
* Windows: in global npm path

### Advantages of global installs

* Accessible anywhere
* Good for tools used across many projects
* Easy to run in terminal

### Disadvantages

* Versions differ across projects
* Can conflict with local tool versions
* Hard to reproduce in CI/CD
* Requires admin privileges on some systems
* Can break when upgrading Node versions
* Harder to maintain clean environments

Because of these issues, global installs are slowly becoming outdated.

## 4. Installing CLI Tools Locally (Inside a Project)

You can install CLI tools locally instead:

```
npm install --save-dev webpack
npm install --save-dev eslint
```

This installs executables into:

```
node_modules/.bin/
```

To run them manually:

```
./node_modules/.bin/eslint .
```

But you rarely do that because npm scripts automatically put local binaries into your PATH.

Example:

```json
"scripts": {
  "lint": "eslint .",
  "build": "webpack"
}
```

Now you can run:

```
npm run lint
npm run build
```

### Advantages of local installs

* Version locked per project
* Fully reproducible
* CI/CD-friendly
* No system pollution
* No version conflicts
* Best practice for modern tools

This is the recommended way to install dev tools.

## 5. What Is npx?

npx is a command runner introduced in npm 5.2+.

It allows you to **run CLI tools without installing them globally**.

Example:

```
npx create-react-app myapp
```

You do not need to install `create-react-app` at all.

npx downloads it temporarily, runs it, and discards it.

### Why npx exists

Before npx:

* You had to install CLI tools globally
* Global installs polluted your system
* Globally installed tools often became outdated
* Version conflicts were common

npx solves all of that.

## 6. How npx Works Internally

When you run:

```
npx tailwindcss init
```

npx will:

1. Check if a **local version** exists in `node_modules/.bin`
2. If not, download a **temporary version** to a cache directory
3. Run the command
4. Remove it (if temporary)

This ensures:

* always the correct version
* no global pollution
* no conflicts across projects

## 7. Using npx with Locally Installed Tools

If a project has:

```
node_modules/.bin/vite
```

Running:

```
npx vite
```

will automatically use the **local** version.

This ensures reproducibility.

Example:

```
npx eslint .
npx jest
npx tailwindcss build
```

## 8. When Should You Use npx?

npx is perfect for:

* running project-specific tools
* scaffolding new projects (React, Next.js, Vue)
* one-time commands
* avoiding global installs
* running tools with trickier versioning

Examples:

```
npx create-next-app@latest myapp
npx prisma migrate dev
npx tsx script.ts
npx shadcn-ui add button
```

These commands are not meant to be installed globally.

## 9. When You Should Avoid npx

Avoid npx when:

* a CLI tool must run extremely frequently
* performance matters (npx may be slightly slower)
* you want explicit version control (prefer local install)
* the tool is mission-critical on your system (rare)

Example where global install is acceptable:

```
npm install -g vercel
```

But even that is debatable, because you can also run:

```
npx vercel
```

## 10. Replacing Global Installs with npx (Recommended)

Global installs:

```
npm install -g nodemon
npm install -g create-react-app
npm install -g typescript
```

Can be replaced with:

```
npx nodemon server.js
npx create-react-app myapp
npx tsc --init
```

This is the modern, recommended approach.

## 11. Understanding PATH Issues (Important)

When you install globally:

* npm adds executables to your PATH
* If the PATH is misconfigured, commands won't run
* Common problem on Windows/macOS

With local installs:

* npm scripts automatically fix PATH
* npx automatically resolves paths
* no system configuration needed

Another reason global installs cause trouble.

## 12. CLI Tools Inside node\_modules/.bin

Every CLI tool installed locally appears here:

```
node_modules/.bin/
```

For example:

* webpack → node\_modules/.bin/webpack
* eslint → node\_modules/.bin/eslint
* vite → node\_modules/.bin/vite

npm scripts automatically include `.bin` in PATH, so:

```
npm run build
```

runs the correct binary without typing full paths.

## 13. Version Conflicts and How npx Solves Them

If you install two different projects:

* Project A uses webpack 4
* Project B uses webpack 5

Global installs cause version clashes.

Local installs + npx avoid this:

```
cd A
npx webpack --version  // 4.x
cd B
npx webpack --version  // 5.x
```

Each project uses the correct version.

## 14. Using npx for One-Time Commands

Some packages are not meant to be installed:

```
npx serve
npx cowsay "hello"
npx degit sveltejs/template myapp
```

These tools are intended to be temporary.

## 15. Security Considerations When Using npx

Because npx can execute remote code:

* always verify package names
* beware of typosquatting (e.g. react-app vs reacta-app)
* use official command generators
* avoid “unknown” npm packages

npm prevents some malicious packages via integrity checks, but caution is still necessary.

## 16. CLI Tools That Should Never Be Global

Do NOT install these globally:

* webpack
* babel
* eslint
* prettier
* tailwindcss
* vite
* typescript
* jest

These should always be project-local.

## 17. CLI Tools That Can Be Global (Optional)

If you prefer:

* nodemon
* vercel
* netlify-cli
* http-server
* expo-cli

But even these can be run via:

```
npx nodemon
npx vercel
npx netlify
```

Best practice: **avoid global installs unless absolutely necessary.**

## 18. Best Practices for CLI Tools

* Prefer local installs over global installs
* Use npx for executing tools
* Use npm scripts for repetitive commands
* Avoid installing build tools globally
* Don’t use sudo for npm installs
* Keep PATH clean
* Rely on project-specific versions
* Use npx for scaffolding projects
* Test across environments using npm ci

## 19. Summary

In this chapter, you learned:

* what CLI tools are
* how global installs work
* why they often cause problems
* how local installs work
* how npx works internally
* when to use npx
* when not to use npx
* how to avoid PATH issues
* how to avoid version conflicts
* how modern workflow prefers npx + local installs
* best practices followed by professional teams

Understanding how to run CLI tools correctly prevents version issues, deployment failures, environment inconsistencies, and the classic “works on my machine” problem.
