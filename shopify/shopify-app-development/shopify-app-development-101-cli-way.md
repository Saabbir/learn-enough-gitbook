# Shopify App Development 101 (CLI Way)

If you’ve ever wondered how Shopify apps are built — or how developers create things like subscription apps, search apps, cart widgets, upsell apps, or product bundles — you’re in the right place.

Shopify app development might sound intimidating, but once you understand the ecosystem and tooling, it becomes one of the **friendliest environments for modern full-stack development**.

This guide is written for **absolute beginners**, with **no assumptions** about prior experience. We’ll cover:

## **📚 Table of Contents**

1. **What Is a Shopify App?**
2. **Why Build Shopify Apps?**
3. **What You Need Before Starting**
4. **Understanding Shopify’s Tech Stack**
5. **Shopify App Development Flow (How Everything Works)**
6. **Introducing Shopify CLI for Apps**
7. **Installing Shopify CLI**
8. **Scaffolding Your First Shopify App**
9. **Running Your App in Development**
10. **What to Learn Next**
11. **Where to Go from Here**



## **1. What Is a Shopify App?**

A Shopify app is a **standalone web application** that connects to a Shopify store to extend its functionality.

Apps can:

✔ Add new backend features&#x20;

✔ Add storefront UI components&#x20;

✔ Modify checkout (Shopify Plus)&#x20;

✔ Automate tasks&#x20;

✔ Provide analytics&#x20;

✔ Integrate external tools&#x20;

✔ Create admin UI extensions&#x20;

✔ Inject new functionality into Shopify’s admin or theme

If you’ve ever installed:

* Klaviyo
* Recharge
* Rebuy
* Judge.me
* Bold Apps

…you’ve used a Shopify app.



## **2. Why Build Shopify Apps?**

Because apps power the Shopify ecosystem.

Developers build Shopify apps to:

* Earn recurring revenue
* Sell apps in the Shopify App Store
* Build custom private apps for clients
* Extend store capabilities
* Build SaaS businesses on top of Shopify
* Integrate external services
* Create custom workflows

Some apps make **millions** in recurring revenue. Some solve niche problems for single merchants.

Either way — the opportunity is huge.



## **3. What You Need Before Starting**

Good news: you don’t need to be an expert developer to begin.

#### ✔ Basic JavaScript knowledge

(variables, functions, async/await)

#### ✔ Basic understanding of web development

(HTML, CSS)

#### ✔ A Shopify Partner account

Free to create: [https://partners.shopify.com](https://partners.shopify.com)

#### ✔ Node.js installed

Recommended: latest LTS version.

#### ✔ A code editor

VS Code is ideal.

That’s it. No need to learn Liquid yet, unless you’re working with theme extensions.



## **4. Understanding Shopify’s App Tech Stack**

Modern Shopify apps use:

#### **Backend:**

* Node.js
* Express
* Remix (new default framework)

#### **Frontend UI:**

* React
* Polaris components (Shopify's official UI toolkit)

#### **Authentication:**

* OAuth
* Shopify App Bridge
* Session tokens

#### **Data:**

* Shopify Admin API
* Shopify GraphQL API
* Webhooks

#### **Extensions:**

* Admin Extensions
* Theme App Extensions
* Checkout UI Extensions
* Product/Cart/Checkout Functions

You **do not need to master all of these on day one**. You’ll learn them naturally as you build.



## **5. How Shopify App Development Works**

Here’s the simple version:

1. You create an app project on your computer
2. Shopify CLI scaffolds the code for you
3. You run the app locally with `shopify app dev`
4. Shopify automatically creates a tunnel URL
5. You install the app on a development store
6. You start coding features
7. Shopify hot-reloads changes into your local store

It’s very developer-friendly.



## **6. Introducing Shopify CLI for Apps**

Shopify CLI is **the main tool** for Shopify app development.

It helps you:

* Scaffold a new app
* Run your dev server
* Generate extensions
* Deploy your app
* Authenticate with Shopify
* Register webhooks
* Work with API endpoints

Think of it as the “Create React App” of Shopify.



## **7. Installing Shopify CLI**

Install globally via npm:

```bash
npm install -g @shopify/cli@latest
```

Check version:

```bash
shopify version
```

Get help:

```bash
shopify help
```

You’re ready to begin.



## **8. Scaffold Your First Shopify App**

Run:

```bash
shopify app init
```

Shopify will ask:

* App name
* App template
* Preferred language (JS or TS)
* Framework (Remix recommended)

#### ⚡ Use a template

Shopify provides official templates:

```bash
shopify app init --template=node
```

Or use the official GitHub repo:

[https://github.com/Shopify/shopify-app-template-node](https://github.com/Shopify/shopify-app-template-node)

This gives you a complete working Shopify app with:

* OAuth
* App bridge
* Polaris UI
* Session handling
* Billing boilerplate
* API setup
* Webhook structure



## **9. Run the App in Development**

Start the app:

```bash
shopify app dev
```

The CLI:

✔ Starts your local dev server&#x20;

✔ Creates a secure tunnel&#x20;

✔ Opens Shopify Partner dashboard&#x20;

✔ Installs app in your dev store&#x20;

✔ Automatically refreshes when you edit code

This is where the magic happens. You now have a real Shopify app running in a store — powered by your code.



## **10. What Should You Learn Next? (Roadmap)**

Here is the perfect beginner roadmap:

### **Step 1 — Shopify Admin API (REST & GraphQL)**

Learn how to:

* Fetch products
* Create/update/delete products
* Manage metafields
* Handle orders
* Read customers
* Write automation logic

### **Step 2 — Polaris (Shopify’s UI library)**

Build beautiful admin pages.

### **Step 3 — Webhooks**

React to store events like:

* Order created
* Product updated
* Cart updated

### **Step 4 — App Extensions**

Learn how to add UI inside stores:

* Theme app extensions
* Checkout UI extensions
* Functions (discounts, validations, etc.)

### **Step 5 — Billing API**

For public apps that charge merchants.

### **Step 6 — Deployment**

Deploy using:

* Fly.io
* Railway
* Render
* Vercel
* Cloudflare Workers

Shopify CLI helps with deployment as well.



## **11. Where to Go From Here**

Once you understand the basics, you can:

#### ✔ Build private apps for clients

#### ✔ Build public apps for the store

#### ✔ Build SaaS products

#### ✔ Extend stores with embedded admin tools

#### ✔ Create automated workflow tools

#### ✔ Build real businesses on Shopify

This is just the beginning.



## **Final Thoughts**

Shopify app development may seem complex from the outside, but once you start, you’ll realize it’s one of the **most developer-friendly ecosystems** available today.

With:

* modern tooling
* React-based UI
* strong documentation
* a massive merchant base
* great earning potential

— it’s one of the best choices for developers in 2025 and beyond.
