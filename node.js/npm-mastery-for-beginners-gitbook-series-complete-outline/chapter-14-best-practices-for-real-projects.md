---
description: >-
  A full set of dependency, versioning, security, and workflow best practices
  used by professional developers.
---

# 📘 CHAPTER 14 — Best Practices for Real Projects

## 1. Introduction

Once you understand:

* npm
* package.json
* semantic versioning
* node\_modules
* lockfiles
* CLI tools
* dependency selection
* workspaces

…the next question is:

**“How do I apply all this knowledge in real projects?”**

It’s one thing to know how npm works.\
It’s another to follow **professional best practices** that prevent:

* deployment failures
* version conflicts
* security issues
* broken builds
* bloated node\_modules
* slow CI pipelines
* large Docker images
* hard-to-maintain codebases

This chapter teaches a complete set of **industry-standard best practices** that senior engineers and large companies follow — but written in a way beginners can understand and apply.

***

## 2. Best Practices for package.json

### Keep package.json clean and organized

Don’t dump random libraries into dependencies.

### Use meaningful npm script names

Examples:

```json
"scripts": {
  "dev": "vite",
  "start": "node index.js",
  "build": "tsc && vite build",
  "lint": "eslint .",
  "format": "prettier --write .",
  "test": "jest"
}
```

Good script naming = better developer experience.

### Always set `"private": true"` unless publishing to npm

This prevents accidental `npm publish`.

### Use the `"engines"` field

Example:

```json
"engines": {
  "node": ">=18"
}
```

This prevents “works on my machine” problems.

### Avoid unnecessary fields

Don’t overpopulate package.json with unused fields like bugs, homepage, etc., unless needed.

***

## 3. Dependency Best Practices

### Install only what you need

Don’t install packages for one-liners.

### Keep dependencies minimal

Fewer dependencies = fewer security risks.

### Prefer built-in Node.js APIs

Node can do more than most beginners know.

Example:\
No need for “is-number,” “mkdirp,” “rimraf,” etc.

### Avoid extremely new packages

Wait until they mature and stabilize.

### Avoid extremely old or abandoned packages

If a package was last updated years ago, consider alternatives.

### Always check:

* weekly downloads
* GitHub activity
* open issues
* release notes

### Don’t install beta or alpha versions in production

Unless you’re intentionally testing them.

***

## 4. Versioning Best Practices

### Follow semver ranges wisely

For production apps:

* Use exact versions (`"axios": "1.7.0"`)
* Or at least pin major/minor (`"~1.7.x"`)

For libraries:

* Use caret ranges (`"^1.7.0"`) to maintain flexibility

### Never blindly upgrade major versions

Always check changelogs and migration guides.

### Use npm-check-updates (ncu)

To safely analyze upgrade paths:

```
npx npm-check-updates
npx npm-check-updates -u
npm install
```

### Keep dependencies updated — but responsibly

Review update logs.\
Test after updating.

***

## 5. Installing & Updating Best Practices

### Do not use `--force` unless absolutely necessary

It can break dependency trees.

### Do not use `--legacy-peer-deps` unless dealing with old projects

Modern projects should not use this flag.

### Do not reinstall randomly

For example:

```
rm -rf node_modules
npm install
```

This should only be done when necessary.

### Use npm ci in CI/CD

Faster, safer, deterministic installs.

```
npm ci
```

### Reinstall only when structure breaks

npm tells you if your dependency tree is corrupted.

***

## 6. Security Best Practices

### Run `npm audit` often

Recommended:

* weekly for active projects
* daily for large teams
* automatically using GitHub Dependabot

### Fix only safe updates

Avoid using:

```
npm audit fix --force
```

unless you fully understand the impact.

### Beware of typosquatting

Check package names carefully:

`express` — correct\
`expres` — malicious

### Enable 2FA if publishing packages

Protects your packages from being hijacked.

### Use npm overrides for fixing insecure nested dependencies

Example:

```json
"overrides": {
  "lodash": "4.17.21"
}
```

***

## 7. node\_modules Best Practices

### Never edit files inside node\_modules

Changes will be wiped during install.

### Use patch-package if patching is required

Example:

```
npm install patch-package
npx patch-package package-name
```

### Do not commit node\_modules to version control

Always ignore it:

```
node_modules/
```

### Know when to delete node\_modules

Delete only if:

* dependency tree is broken
* npm explicitly recommends it
* migrating toolchains

***

## 8. Lockfile Best Practices

### Always commit package-lock.json

Teams rely on it for reproducible builds.

### Never edit it manually

npm manages it automatically.

### Do not delete it unless absolutely necessary

Removing it removes version locks.

### Use npm ci in production

It honors the lockfile strictly.

***

## 9. Script & Tooling Best Practices

### Use npm scripts instead of global CLI tools

Example:

Instead of global nodemon:

```
npx nodemon
```

Or add to devDependencies:

```
npm install -D nodemon
npm run dev
```

### Add common scripts

* lint
* format
* test
* build
* dev
* clean

### Group scripts for monorepos

Example:

```
npm run dev --workspace web
npm run dev --workspace api
```

### Use npm-run-all or turbo for script orchestration

Helps run scripts in parallel.

***

## 10. Project Organization Best Practices

### Use environment-specific configs

Examples:

* `.env.development`
* `.env.production`

Never expose secrets.

### Use `.gitignore` appropriately

Typical entries:

```
node_modules/
dist/
.env
.cache/
```

### Add README documentation

Explain:

* setup
* commands
* project structure
* environment variables

### Keep consistent formatting

Use Prettier, ESLint, EditorConfig.

### Use TypeScript for large or long-lived projects

Better maintainability and fewer runtime bugs.

***

## 11. CI/CD Best Practices

### Use npm ci for installs

Faster and strict.

### Cache node\_modules or the npm cache

Significantly improves build speed.

### Run linting and tests automatically

Prevent broken code from merging.

### Avoid rebuilding dependency trees unnecessarily

Use caching strategies.

***

## 12. Docker Best Practices for Node.js

### Use minimal base images

Example:

```
node:18-slim
```

### Copy only necessary files

Avoid copying entire workspace.

### Use multi-stage builds

Build in one stage, run in another.

### Always run:

```
npm ci --only=production
```

Not `npm install`.

### Use environment variables for configuration

Avoid hardcoding secrets.

***

## 13. Monorepo Best Practices

### Structure workspaces clearly:

```
apps/
packages/
```

### Put shared configs at the root

* tsconfig.base.json
* eslint config
* prettier config

### Use workspace scripts efficiently

Example:

```
npm run dev --workspaces
```

### Avoid duplicated dependencies

Use hoisted dependencies when possible.

***

## 14. Testing Best Practices

### Write automated tests

Use Jest, Vitest, Mocha, etc.

### Test critical paths regularly

Avoid regression.

### Run tests in CI

Prevents broken code from merging.

***

## 15. Performance Best Practices

### Remove unused dependencies

Use:

```
npx depcheck
```

### Prefer pnpm for large projects

Better performance and smaller node\_modules.

### Use caching in CI

Significant speed improvements.

### Keep dependencies up-to-date but stable

Balance between innovation and reliability.

***

## 16. Summary

In this chapter, you learned the complete set of best practices for real-world npm and Node.js projects:

* how to maintain clean and safe package.json files
* how to choose and version dependencies responsibly
* how to manage installations and updates safely
* how to secure your project
* how to use lockfiles correctly
* how to organize scripts and tooling
* how to structure projects and monorepos
* how to optimize CI/CD and Docker environments
* how to improve performance and maintainability

Following these best practices prevents the most common problems in JavaScript development:

* dependency hell
* breaking changes
* deployment failures
* bloated node\_modules
* inconsistent environments
* security vulnerabilities
* messy project structure

This chapter completes your foundational understanding of npm and dependency management at a professional level.
