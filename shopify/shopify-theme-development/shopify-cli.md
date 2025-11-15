---
description: >-
  A complete, beginner-friendly guide to working with Shopify themes using the
  latest Shopify CLI.
---

# Shopify CLI for Theme Developers

**Introduction — Every Shopify Developer’s Turning Point**

Whether you’re just starting with Shopify theme development or you’ve been editing Liquid files for years, there’s a moment when you realize:

> **“I can’t build Shopify themes efficiently without Shopify CLI.”**

Shopify CLI is no longer optional.\
It’s the engine that powers modern theme development — live previews, fast syncing, best-practice checks, deployments, environment setups, everything.

And yet…

* The CLI changes often.
* Documentation moves fast.
* Commands get deprecated.
* Authentication flow changes.
* Beginners get lost quickly.

So this article is your **permanent reference** — a complete, easy-to-understand guide that you can return to anytime you're confused or stuck.

This is the **2025 complete guide**, updated for **Shopify CLI v3.57+**, where the login system changed significantly.

Let’s start from the beginning.

***

## **What Exactly Is Shopify CLI? (And Why Should You Care?)**

Shopify CLI is a **command-line tool** that helps you create, preview, test, lint, modify, and deploy Shopify themes from your local machine.

Without Shopify CLI:

❌ You manually upload ZIPs\
❌ You lose time with repeated uploads\
❌ You have no live preview\
❌ You risk breaking live themes\
❌ You can't use modern workflows (Git, CI/CD, automation)

With Shopify CLI:

✅ Instant preview via dev server\
✅ Automatic syncing of your local files\
✅ Safe development themes\
✅ Theme code quality checks\
✅ Simplified deployments\
✅ Version control-friendly workflow\
✅ Smooth collaboration

In short:

> **Shopify CLI turns Shopify theme development into real software development.**

***

## **System Requirements (Don’t Skip This)**

Shopify CLI requires:

#### **Node.js**

* Version **18.20+**
* OR **20.10+**

Check:

```
node -v
```

***

#### **Git**

* Version **2.28.0+**

Check:

```
git --version
```

Git is used under the hood for templates and workflows.

***

## **Installing Shopify CLI**

Install globally:

```
npm install -g @shopify/cli@latest
```

Verify version:

```
shopify version
```

Upgrade:

```
shopify upgrade
```

Help:

```
shopify help
```

***

## **IMPORTANT: Shopify CLI Authentication Completely Changed (2024–2025)**

Older tutorials will tell you:

```
shopify login --store your-store.myshopify.com
```

❌ **This command no longer exists for themes.**\
❌ It will NOT work on CLI v3.57+.

#### Shopify replaced it with a much simpler flow:

### **Login = Automatic**

Just run:

```
shopify theme dev
```

If you're not logged in, Shopify CLI:

* Opens your browser
* Asks you to log in
* Grabs your authentication token
* And you’re in

No store flags needed.\
No manual login.\
Just pure simplicity.

***

### **Logout = Manual**

If you want to switch accounts:

```
shopify auth logout
```

Then start your dev session again:

```
shopify theme dev
```

***

## **The Shopify CLI Landscape (Themes Only)**

Shopify CLI has tools for:

* Apps
* Auth
* Config
* Extensions
* Storefronts (Hydrogen)
* **Themes** ← _Your focus_

So from this point on…

#### ✔️ We only focus on **theme development commands**

#### ✔️ No app development

#### ✔️ No hydrogen

#### ✔️ No extensions

This keeps the article relevant, clean, and confusion-free.

***

## **Creating Shopify Themes Using Shopify CLI**

If you're starting a new custom theme or learning Liquid, this is the starting point.

### **1. Initialize a new theme**

```
shopify theme init
```

This creates a brand-new project using Shopify’s official **Dawn** theme as your starter.

To specify a folder name:

```
shopify theme init my-awesome-theme
```

This gives you:

* `/layout` files
* `/sections`
* `/templates`
* `/snippets`
* `/assets`
* `/locales`

Perfect structure. Ready for development.

***

## **Running the Dev Server (Your Daily Workspace)**

This command is the **heart** of modern theme development:

```
shopify theme dev
```

What it does:

#### 💚 Local → Shopify instant syncing

Edits your file locally → refreshes preview instantly.

#### 💚 Creates a secure preview URL

You can share it with clients, PMs, QA testers.

#### 💚 Automatically logs you in

No login command needed anymore.

#### 💚 Shows logs in your terminal

Helpful when debugging sections, metafields, logic errors.

#### 💚 Safe development theme

It uses a temporary theme in the store (not live).

This is what makes Shopify theme development feel modern and enjoyable.

***

## **Uploading Themes (Deploying to Shopify)**

To upload your local theme to Shopify:

```
shopify theme push
```

This lets you:

* Create a new theme
* Select an existing theme
* Replace a theme safely

If you're automating or confident:

```
shopify theme push -f
```

⚠️ **Warning:** `-f` overwrites without asking.

***

## **Downloading Themes (Working With an Existing Store)**

If you're joining a project or the client edited the theme without you:

```
shopify theme pull
```

This downloads the entire theme code into your local folder.

***

## **Listing Themes on a Store**

Useful for finding theme IDs:

```
shopify theme list
```

You'll see:

* Theme ID
* Theme Name
* Role (live, draft, development)

***

## **Opening the Online Theme Editor (for quick testing)**

```
shopify theme open
```

This opens the theme editor directly for the theme you're working on.

***

## **Checking Your Theme (Quality Control)**

Shopify provides **Theme Check**, a tool that scans your theme for problems.

Run:

```
shopify theme check
```

It finds:

* Liquid errors
* Deprecated tags
* Missing schema
* Failed best practices
* Performance issues
* Translation errors
* Broken includes

This command prevents disasters before deployment.\
Use it often.

***

## **The Complete Shopify Theme Development Workflow**

Here's what a real developer workflow looks like:

***

### **1. Clone or init theme**

```
shopify theme init
```

or

```
shopify theme pull
```

***

### **2. Start development**

```
shopify theme dev
```

Edit → Save → Instantly preview.

***

### **3. Check code quality**

```
shopify theme check
```

Fix warnings now instead of during panic later.

***

### **4. Version control**

```
git add .
git commit -m "Feature: added product card hover interaction"
```

***

### **5. Push to Shopify**

```
shopify theme push
```

***

### **6. Open editor**

```
shopify theme open
```

Adjust settings, metafields, blocks, etc.

***

## **Common Errors & Their Fixes (Bookmark This Section)**

#### ❌ _“shopify login not found”_

This is expected.\
Login no longer exists.

➡️ Run:

```
shopify theme dev
```

***

#### ❌ Live reload not working

Restart:

```
shopify theme dev
```

***

#### ❌ Permission denied / wrong store

Logout:

```
shopify auth logout
```

Start dev again.

***

#### ❌ Didn’t pull the latest theme changes

Run:

```
shopify theme pull
```

***

#### ❌ Theme too large

Remove:

* Large videos
* Unused media
* Dev junk files

***

#### ❌ “File ignored” errors

Shopify ignores:

* `node_modules/`
* `.git`
* Lock files
* System files

This is normal.

***

## **Best Practices for Shopify CLI (From Real Theme Developers)**

* Always use a **development theme**, never the live theme
* Use `theme check` before every push
* Keep Node.js and CLI updated
* Use Git ALWAYS
* Avoid mixing local edits with online editor edits
* Push early, push often (like saving your progress)
* Separate features into branches
* Clean unused dev themes from the store admin
* Create `.env.example` for collaborators
* Read CLI logs — they reveal a lot

***

## **Conclusion — Shopify CLI is Your Superpower**

If you learn Shopify CLI deeply, theme development stops being a guessing game.\
It becomes a professional, predictable workflow:

* You build faster
* You deploy safely
* You debug smarter
* You collaborate better
* You feel confident

This CLI is not just a tool — it’s your **daily companion**, the backbone of modern Shopify theme development.

{% hint style="info" %}
Learn more about Shopify CLI at [https://shopify.dev/docs/api/shopify-cli](https://shopify.dev/docs/api/shopify-cli)
{% endhint %}

