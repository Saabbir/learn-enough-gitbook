---
description: >-
  A complete guide to evaluating npm packages for security, reliability,
  performance, and long-term maintainability.
---

# 🔟 CHAPTER 10 — Choosing the Right Package (Safety Checklist)

## 1. Introduction

npm contains **millions of packages**, but not all of them are safe, stable, or actively maintained.\
Some packages are:

* outdated
* abandoned
* poorly maintained
* insecure
* bloated
* duplicated under different names
* created by inexperienced developers
* created only for fun (e.g., packages with 1-line functions)

If you simply run:

```
npm install random-package
```

this can:

* break your app
* expose security vulnerabilities
* inflate bundle sizes
* add unnecessary complexity
* cause version conflicts
* create long-term maintenance debt

This chapter teaches you **how to choose npm packages safely and intelligently**, just like a senior developer or architect.

## 2. Why Choosing the Right Package Matters

Dependencies become part of _your codebase_.\
Once you install a package:

* you trust its author
* you trust its code
* you trust its transitive dependencies
* you trust its maintenance

Your app becomes vulnerable to:

* supply-chain attacks
* unpatched security flaws
* breaking changes
* huge node\_modules
* poor performance in production

Being intentional about which packages you install is one of the most important skills in modern JavaScript development.

## 3. Where to Look Before Installing a Package

Before installing anything, check:

### npm Registry Page

* [https://www.npmjs.com/package/](https://www.npmjs.com/package/)\<package-name>\
  Shows version history, weekly download stats, maintainers, README.

### GitHub Repository

Code, issues, commits, releases, PR activity.

### CHANGELOG or Release Notes

Shows how actively the package is maintained.

### The README.md

This is the “first impression” of any project.

Look for:

* clear instructions
* active updates
* recent examples
* clear versioning

If the README is unclear, outdated, or messy — treat it as a warning sign.

## 4. The 10-Point Package Safety Checklist (Industry Standard)

Use this checklist **before installing any package**.

### 1. Is the package actively maintained?

Check the “Last Published” date on npm.

* Updated this week? Excellent.
* Updated this year? Probably okay.
* Last update 4 years ago? Avoid.

Modern JavaScript evolves quickly — outdated packages often break.

### 2. Are there enough weekly downloads?

Downloads = trust.

Example guidelines:

* 1M+ weekly downloads → Very stable
* 100K+ weekly downloads → Very good
* 10K+ weekly downloads → Acceptable
* <100 weekly downloads → Avoid unless necessary

Low downloads may indicate the package is unused or untrusted.

### 3. Is the GitHub repository active?

Check:

* frequency of commits
* open issues count
* pull requests
* release tags

If the last commit is from 2017 — avoid.

### 4. Does the package have proper versioning (SemVer)?

Check if versions look stable:

Good signs:

* consistent increments
* meaningful release notes
* no sudden major version jumps without explanation

Bad signs:

* version stuck at “0.x” forever
* too many patch releases in short time

### 5. How many dependencies does it have?

Too many dependencies = risk.

A good package often depends on **very few** other packages.

Avoid:

* packages with >50 dependencies
* tiny helper packages like “is-odd”, “left-pad”
* packages with deep dependency trees

The fewer dependencies, the fewer things that can break.

### 6. Is the maintainer trustworthy?

Check:

* Are they known in the ecosystem?
* Do they maintain other well-known packages?
* Is the maintainer active on GitHub?

If the account:

* has 0 followers
* no activity
* no profile photo
* no other contributions

proceed with caution.

### 7. Does the package have TypeScript support?

Even if your project isn't using TypeScript:

Built-in TS types often indicate:

* better code quality
* better maintenance
* better editor autocompletion

If TS types are missing but available on DefinitelyTyped:

```
npm install @types/package-name
```

then it’s still acceptable.

### 8. Check for security vulnerabilities

Use:

```
npm audit the-package-name
```

or check the npm registry for:

* vulnerabilities
* malicious behavior
* suspicious release history

### 9. Compare alternatives

Often there are safer, more popular, more modern alternatives.

Use Google or GitHub search:

* “best validation library Node.js”
* “fastest HTTP client Node”

Choose packages with strong communities.

### 10. Try to avoid tiny libraries for trivial tasks

Node.js already includes:

* path utilities
* URL parsing
* Buffer manipulation
* crypto algorithms
* timers

Avoid installing packages for tasks that take 1–5 lines of code.

Examples of unnecessary packages:

* is-even
* is-number
* left-pad
* array-flatten

Instead, write the function yourself.

## 5. How to Evaluate a Package Like a Senior Developer

Let’s analyze a real package step-by-step:

Example package: `axios`

### 1. Downloads

30M+ weekly.

Confidence: ✔✔✔✔✔

### 2. Last updated

Recently updated.

Confidence: ✔✔✔✔✔

### 3. GitHub activity

Active maintainers, active issues.

Confidence: ✔✔✔✔✔

### 4. Dependencies

Very few.

Confidence: ✔✔✔✔✔

### Result: Safe, mature, trustworthy.

Now check a random package:

Example package: `cool-http-wrapper`

### 1. Downloads

27 per week.

Confidence: ❌

### 2. Last updated

3 years ago.

Confidence: ❌

### 3. GitHub

1 contributor, no commits for years.

Confidence: ❌

### 4. Dependencies

87 packages.

Confidence: ❌

### Result: Avoid completely.

## 6. Red Flags (Avoid These Packages)

Avoid packages with these patterns:

* low weekly downloads
* unmaintained repos
* tiny one-line helpers
* vague or missing documentation
* no license
* too many dependencies
* version stuck at “0.1.x” for years
* minified code with no source
* no TypeScript typings
* unknown authors
* complex dependency chains
* suspiciously frequent patch releases

## 7. Package Size and Bundle Impact

Packages can slow down your application.

### For servers:

Too many dependencies = large Docker images.

### For frontend apps:

Large libraries = slow website.

Always check:

* bundlephobia.com
* unpkg.com
* bundle size in docs

If a package adds >50KB to your bundle, evaluate alternatives.

## 8. Checking License Compatibility

Before using a package commercially, ensure its license is acceptable.

Common licenses:

* MIT → best, permissive
* ISC → MIT-like
* Apache 2.0 → good
* GPL → restrictive, often not allowed in commercial products
* AGPL → extremely restrictive

If unclear — avoid.

## 9. Security Considerations

Check npm audit:

```
npm audit
```

Read advisory details.

Also check the GitHub repository:

* recent security issues
* reported vulnerabilities
* pending patches

If the maintainer ignores security reports — avoid.

## 10. When to Prefer Built-In Node.js Modules

Node.js comes with many built-in modules:

* crypto
* fs
* path
* http
* zlib
* url
* buffer

Before installing a package, ask:

“Can I do this with built-in Node.js features?”

Often the answer is yes.

## 11. When to Write Your Own Code Instead of Installing a Package

Good reasons to write a function yourself:

* it’s a small task
* you want full control
* you want to reduce dependencies
* the alternative package is bloated
* it removes long-term maintenance risk

Examples:

* deep clone → write a simple utility
* string manipulation → native JS
* small validation → write a function

Avoid “micro-packages” unless absolutely necessary.

## 12. Questions to Ask Before Installing a Package

Ask yourself:

* Is this package actively maintained?
* Is it necessary?
* Is there a simpler alternative?
* Will it bloat my app?
* What happens if the maintainer abandons it?
* Does it introduce security risks?
* Does it integrate well with existing dependencies?
* What license does it use?

If you can’t justify installing it, do not install it.

## 13. A Step-by-Step Safe Installation Workflow

1. Search npm for the official or most popular choice
2. Check download counts
3. Check recent updates
4. Check GitHub activity
5. Check issues & PRs
6. Check dependencies count
7. Compare alternatives
8. Read the documentation
9. Verify license
10. Install only when confident

Follow this workflow every time.

## 14. Summary

In this chapter, you learned:

* how to evaluate npm packages before installing them
* how to identify trustworthy libraries
* how to avoid harmful or abandoned packages
* how to assess popularity, activity, security, and quality
* how to compare alternatives intelligently
* when to write your own code instead of using a dependency
* how to avoid unnecessary micro-packages
* best practices for choosing dependencies in production

By following this chapter, you greatly reduce:

* breaking changes
* dependency bloat
* security vulnerabilities
* long-term maintenance issues
* performance problems

This is exactly how senior engineers evaluate dependencies before adding them to mission-critical software.
