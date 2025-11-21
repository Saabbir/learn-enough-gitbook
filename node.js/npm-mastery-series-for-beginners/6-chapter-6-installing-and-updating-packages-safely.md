---
description: >-
  A complete guide to installing, updating, removing, and managing dependencies
  in professional Node.js projects.
---

# 6️⃣ CHAPTER 6 — Installing & Updating Packages Safely

## 1. Introduction

Every Node.js project depends on external packages. Installing a package is easy, but installing it **safely, correctly, and responsibly** is a whole different skill.

Many developers unknowingly:

* break their projects by updating incorrectly
* install dangerous or unmaintained packages
* misuse devDependencies and dependencies
* create unstable builds
* introduce security vulnerabilities

This chapter teaches you **how to install, update, and manage packages safely**, using best practices followed by professional development teams.

You’ll learn:

* how npm install really works
* how to install packages correctly
* how to update them safely
* how to avoid dependency hell
* how to inspect package health before installing
* how to remove packages cleanly
* what commands to avoid
* how package.json and package-lock.json interact during installs

This is one of the most important chapters in the entire series.

## 2. Installing Packages: The Basics

Installing a package is simple:

```
npm install package-name
```

This does three things:

1. Downloads the package from npm
2. Adds it to `node_modules`
3. Updates `package.json` and `package-lock.json`

By default, `npm install` installs the **latest version within the allowed semver range**.

## 3. Installing a Specific Version

If you want to be explicit:

```
npm install express@4.19.1
```

You can also specify ranges:

```
npm install lodash@^4.17.0
npm install axios@~1.6.0
```

Specific versions are ideal for:

* production applications
* microservices
* backend APIs

## 4. Installing as a Development Dependency

Development dependencies are used only while coding, not in production.

Examples:

* TypeScript
* Babel
* Jest
* ESLint
* Nodemon

Install them like this:

```
npm install --save-dev eslint
```

or shorthand:

```
npm i -D eslint
```

These go into `"devDependencies"` inside package.json.

## 5. Installing as a Global Package

Global installations should be used **sparingly**.

```
npm install -g nodemon
npm install -g vercel
```

Global packages:

* are installed system-wide
* may conflict across projects
* often require sudo on Linux
* can cause version mismatch issues

Most modern workflows use **npx** instead of global installs.

## 6. Installing a Package Without Saving to package.json

Sometimes you need to install something temporarily:

```
npm install <package> --no-save
```

This is rarely needed, but useful for quick experiments.

## 7. Installing All Dependencies in a Project

When you clone a repository:

```
npm install
```

npm reads package-lock.json and installs exact versions.

This is why package-lock.json is essential.

## 8. Installing Dependencies for Production Only

If you want only production packages:

```
npm install --production
```

This will skip devDependencies.

Useful for:

* Docker builds
* server deployments
* lightweight environments

## 9. Installing Peer Dependencies (Common Pain Point)

If you see an error like:

```
requires a peer of react@^18.0.0 but none is installed
```

You must manually install it:

```
npm install react@18
```

Peer dependencies are explained deeply in Chapter 9.

## 10. Updating Packages Safely

Updating packages is dangerous when done incorrectly.\
This is where many beginners break their projects.

### Checking outdated packages:

```
npm outdated
```

This shows:

* Current version installed
* Wanted version
* Latest available version

Example output:

```
axios    1.6.0   1.6.5   1.7.0
```

Meaning:

* Current: your version now
* Wanted: allowed by package.json
* Latest: newest version published

### Updating within allowed semver range:

```
npm update
```

This updates minor and patch versions only.

### Updating a specific dependency:

```
npm install axios@latest
```

or install a specific version:

```
npm install axios@1.7.0
```

### Updating all dependencies safely:

```
npx npm-check-updates -u
npm install
```

This upgrades all semver ranges in package.json.

Always review updates individually in production systems.

## 11. The Danger of Updating Everything at Once

If you run:

```
npm update
```

npm will:

* install newer minor versions
* install newer patch versions
* NOT install new major versions

This can still break projects because minor versions can introduce unexpected changes.

When updating all at once:

* test thoroughly
* update one major dependency at a time
* read changelogs
* follow migration guides

For large apps, blindly updating everything is a bad practice.

## 12. Updating Major Versions

Major versions almost always require:

* reading release notes
* updating code
* handling breaking changes

Example:

```
npm install express@5
```

Before upgrading:

* check API changes
* check deprecated methods
* update your code manually

This is where most breakage happens.

## 13. The Safe Update Workflow (Professional Standard)

A safe workflow followed in real companies:

1. Create a separate Git branch
2. Run `npm outdated`
3. Update one dependency at a time
4. Read the changelog for each update
5. Run tests
6. Manually test critical paths
7. Push code
8. Let CI run automated tests
9. Review
10. Merge

Never update dependencies directly on main/master.

## 14. Removing Dependencies Safely

To remove a package:

```
npm uninstall package-name
```

This:

* removes it from node\_modules
* removes it from package.json
* updates the lockfile

Then verify your project doesn’t break.

## 15. Checking What Packages Are Installed

List top-level dependencies:

```
npm ls --depth=0
```

List full dependency tree:

```
npm ls
```

Check for unused dependencies:

```
npx depcheck
```

This is extremely useful to clean your project.

## 16. Avoiding Dependency Hell

“Dependency hell” happens when:

* too many layers of dependencies
* conflicting versions
* incompatible peer deps
* outdated packages
* multiple libraries depend on outdated versions

To avoid this:

* remove unused dependencies
* keep your dependency list small
* avoid installing random tiny libraries for trivial tasks
* prefer built-in Node.js utilities where possible
* audit dependencies regularly

## 17. Avoiding Dangerous Commands

Beginners often break their projects using commands like:

```
npm install --force
npm audit fix --force
```

These force npm to:

* override dependency trees
* install incompatible versions
* break peer dependencies
* ignore version constraints

Use `--force` only if you understand the implications.

## 18. Using npm ci for Stable Environments

For CI/CD:

```
npm ci
```

npm ci:

* removes node\_modules
* installs EXACT versions from lockfile
* does NOT modify package.json
* fails if lockfile and package.json disagree
* is much faster than npm install

This ensures stable production builds.

## 19. How npm Decides Which Version to Install

npm follows these steps:

1. Read package.json semver range
2. Check package-lock.json for exact versions
3. Compare with npm registry
4. Install the version locked in package-lock.json
5. Only update if user requests it

This ensures consistency across machines.

## 20. Common Mistakes to Avoid

Beginner mistakes:

* installing packages globally unnecessarily
* installing dependencies as devDependencies incorrectly
* deleting package-lock.json
* blindly running npm update
* editing node\_modules directly
* installing packages with random tutorials
* letting versions drift out of sync

Avoid these to keep your project stable.

## 21. Best Practices for Installing & Updating Packages

* Always review dependencies before installing
* Install only what you really need
* Prefer built-in Node.js features
* Commit package-lock.json
* Use exact versions for production apps
* Run npm audit regularly
* Use npm ci for CI/CD
* Read changelogs before updating
* Update dependencies one by one
* Remove unused packages frequently

## 22. Summary

In this chapter, you learned:

* how npm install really works
* how to install dependencies correctly
* how to update them safely
* how to remove them cleanly
* how to avoid dependency hell
* how to prevent breaking changes
* why you must treat updates carefully
* the difference between installing local, global, and dev dependencies
* how to assess outdated packages
* why npm ci is the best choice for deployments

You now understand the practical side of managing packages in professional projects.
