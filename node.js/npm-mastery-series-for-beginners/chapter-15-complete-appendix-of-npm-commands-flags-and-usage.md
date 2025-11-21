---
description: >-
  A full reference guide to all essential npm commands, explanations, examples,
  and advanced flags.
---

# 📘 CHAPTER 15 — Complete Appendix of npm Commands, Flags & Usage

## 1. Introduction

npm has many commands, some commonly used, some rarely used, and some extremely powerful.\
For beginners, the challenge is often:

* remembering what each command does
* knowing when to use it
* understanding common flags
* avoiding dangerous commands
* using npm features efficiently

This appendix is your **one-stop reference** for practically every npm command you will need throughout your Node.js journey — from basic usage to advanced techniques used in production environments.

Every command includes:

* explanation
* example
* common flags
* warnings (if needed)
* best practices

Let’s begin.

***

## 2. Installation & Project Setup Commands

### npm init

Create a new project interactively.

```
npm init
```

### npm init -y

Create a new project with default settings.

```
npm init -y
```

Creates a `package.json` automatically.

### npm root

Shows the location of your project’s node\_modules.

```
npm root
```

### npm root -g

Shows the global node\_modules directory.

```
npm root -g
```

***

## 3. Installing Packages

### npm install (or npm i)

Install all dependencies from package.json.

```
npm install
```

### npm install \<package>

Install a package into dependencies.

```
npm install express
```

### npm install -D \<package>

Install into devDependencies.

```
npm install -D eslint
```

### npm install \<package>@\<version>

Install a specific version.

```
npm install axios@1.6.0
```

### npm install \<git repo>

Install directly from GitHub.

```
npm install github:username/repo
```

### npm install --save-optional

Add to optionalDependencies.

```
npm install sharp --save-optional
```

### npm install --no-save

Install without tracking in package.json.

```
npm install lodash --no-save
```

### npm install -g \<package>

Install globally.

```
npm install -g vercel
```

***

## 4. Removing Packages

### npm uninstall \<package>

Remove a dependency.

```
npm uninstall axios
```

### npm uninstall -D \<package>

Remove from devDependencies.

```
npm uninstall -D jest
```

### npm uninstall -g \<package>

Remove global installs.

```
npm uninstall -g nodemon
```

***

## 5. Updating Packages

### npm update

Update dependencies within allowed semver ranges.

```
npm update
```

### npm update \<package>

Update only one package.

```
npm update react
```

### npm outdated

Show outdated dependencies.

```
npm outdated
```

### npm install \<package>@latest

Install the latest version.

```
npm install express@latest
```

### npm-check-updates (external tool, but important)

List available upgrades.

```
npx npm-check-updates
npx npm-check-updates -u
npm install
```

***

## 6. Running Scripts

Defined in package.json under `"scripts"`.

### npm run \<script>

Run a custom script.

```
npm run dev
```

### npm start

Special case: runs `"start"` script.

```
npm start
```

### npm test

Special case: runs `"test"` script.

```
npm test
```

### npm run-script (same as npm run)

Explicit equivalent.

```
npm run-script build
```

***

## 7. Using npx

### npx \<command>

Run a package without installing it globally.

```
npx create-react-app myapp
```

### npx \<local-tool>

Run local binaries in node\_modules/.bin.

```
npx eslint .
```

***

## 8. Workspaces (Monorepo) Commands

### npm install -w \<workspace>

Install a dependency in a specific workspace.

```
npm install express -w api
```

### npm run \<script> -w \<workspace>

Run scripts inside a workspace.

```
npm run dev -w web
```

### npm run \<script> --workspaces

Run the script across all workspaces.

```
npm run build --workspaces
```

### npm list -w \<workspace>

Show dependency tree for a workspace.

```
npm list -w api
```

***

## 9. Exploring Dependencies

### npm list

Show full dependency tree.

```
npm list
```

### npm list --depth=0

Show only top-level packages.

```
npm list --depth=0
```

### npm explore \<package>

Open a shell inside the package directory.

```
npm explore express
```

### npm view \<package>

Get package metadata.

```
npm view react
```

Useful for checking:

* latest version
* dependencies
* maintainers
* scripts

### npm view \<package> version

Show latest version.

```
npm view next version
```

***

## 10. Security Commands

### npm audit

Scan for vulnerabilities.

```
npm audit
```

### npm audit fix

Apply safe fixes.

```
npm audit fix
```

### npm audit fix --force

Apply all fixes, including breaking ones.

```
npm audit fix --force
```

Use carefully.

***

## 11. Lockfile & Consistency

### npm ci

Clean install using package-lock.json.

```
npm ci
```

Key features:

* faster than npm install
* ideal for CI/CD
* removes node\_modules and reinstalls cleanly
* fails if package.json and lockfile mismatch

### npm dedupe

Removes duplicate packages in node\_modules.

```
npm dedupe
```

Useful when dependency trees become messy.

***

## 12. Publishing Packages

### npm login

Authenticate into npm registry.

```
npm login
```

### npm publish

Publish the current package.

```
npm publish
```

### npm publish --dry-run

Simulate the publish process.

```
npm publish --dry-run
```

### npm version \<patch|minor|major>

Automatically update the version field.

```
npm version patch
npm version minor
npm version major
```

### npm unpublish

Remove a published package (strongly discouraged).

```
npm unpublish
```

***

## 13. Config & Settings

### npm config get

View npm config values.

```
npm config get registry
```

### npm config set

Set configuration values.

```
npm config set init-author-name "Saabbir Hossain"
```

### npm set registry

Change registry (example: private registry).

```
npm set registry https://registry.npmjs.org/
```

### npm config delete

Remove a config key.

```
npm config delete init-license
```

***

## 14. Cache & Cleanup

### npm cache clean

Clear the npm cache.

```
npm cache clean --force
```

### npm cache verify

Verify integrity and check for corruption.

```
npm cache verify
```

***

## 15. Environment & Debugging

### npm doctor

Run diagnostic checks.

```
npm doctor
```

### npm help

Show help documentation.

```
npm help
```

### npm help \<command>

Get command-specific help.

```
npm help install
```

### npm bin

Show where npm places executable binaries.

```
npm bin
```

### npm bin -g

Show global binary directory.

```
npm bin -g
```

***

## 16. Global System Commands

### npm list -g --depth=0

List global packages.

```
npm list -g --depth=0
```

### npm outdated -g

Check outdated global packages.

```
npm outdated -g
```

### npm update -g

Update global tools.

```
npm update -g
```

***

## 17. Dangerous Commands (Use Carefully)

### npm audit fix --force

Forces updates, may break your app.

### npm install --force

Bypass dependency checks, risky.

### npm install --legacy-peer-deps

Ignores peer dependency rules; only for legacy projects.

### npm unpublish

Dangerous for published libraries.

### npm cache clean --force

Should only be used when cache is corrupted.

***

## 18. Summary

In this appendix, you explored:

* all core npm commands
* package installation and removal
* workspace commands
* publishing workflows
* dependency tree exploration
* security auditing
* lockfile commands
* global installations
* npm configuration
* debugging tools
* dangerous commands to avoid

This appendix forms your **complete reference manual**.\
You can now confidently work with npm at any level — beginner, intermediate, or advanced.
