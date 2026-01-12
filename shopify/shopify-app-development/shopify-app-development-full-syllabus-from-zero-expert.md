# 🧠 Shopify App Development — Full Syllabus (From Zero → Expert)

## PHASE 0 — Foundation Mindset (Read Once, Remember Forever)

#### Goal

Understand **what kind of software** a Shopify app actually is.

#### Outcomes

You can explain:

* Why Shopify apps **must** have a backend
* Why access tokens are never in frontend JS
* Why OAuth exists
* Why apps need databases

📌 _No code yet._



## PHASE 1 — Backend Fundamentals (Non-Negotiable Core)

> **This phase turns you from “frontend dev” → “full-stack capable”**

#### 1. Node.js Fundamentals

**You must master:**

* Node runtime vs browser JS
* Event loop
* Async / await
* Environment variables

🔧 Practice:

* Build a Node script
* Use `process.env`
* Read/write files

#### 2. HTTP Deep Dive

You will learn:

* What actually happens when you visit a URL
* Headers, cookies, status codes
* GET vs POST vs PUT
* JSON request bodies

🔧 Practice:

* Make raw HTTP requests
* Inspect headers manually

#### 3. Express.js (Server Brain)

Learn **Express** properly:

* Routing
* Middleware
* Request lifecycle
* Error handling

🔧 Practice:

* Build a small API
* Create routes
* Return JSON



## PHASE 2 — Authentication, Sessions & Security 🔐

> **This phase unlocks Shopify**

#### 4. OAuth 2.0 (Core Theory)

Before Shopify:

* Authorization Code Flow
* Client ID / Secret
* Redirect URIs
* Scopes

📌 You must be able to draw the OAuth flow from memory.

#### 5. Sessions & Cookies

Learn:

* Cookie vs session vs JWT
* HTTP-only cookies
* CSRF
* Why embedded apps behave differently

🔧 Practice:

* Build login system (non-Shopify)
* Store session server-side

#### 6. Web Security Basics

You’ll understand:

* HMAC signatures
* Request verification
* Why Shopify signs requests



## PHASE 3 — Databases & Persistence 🗄️

> Shopify apps **remember things**

#### 7. Database Fundamentals

Learn:

* SQL vs NoSQL
* Tables / collections
* Relationships
* Indexes

Recommended:

* Start with SQLite
* Move to PostgreSQL or MongoDB

🔧 Practice:

* Store users
* Store tokens
* CRUD operations



## PHASE 4 — Shopify Platform Fundamentals 🧩

> Now you’re finally ready for Shopify.

#### 8. Shopify Ecosystem

Understand:

* Shopify Admin
* App types
* Public vs custom apps
* Embedded apps
* Shopify App Store review rules

#### 9. Shopify APIs

Learn:

* REST vs GraphQL
* Admin API
* Storefront API
* Rate limits

🔧 Practice:

* Fetch products
* Create products
* Update products



## PHASE 5 — Shopify OAuth (Hands-On, No CLI)

> This is where most devs fail. You won’t.

#### 10. Shopify OAuth Flow (Manual)

You will build:

* `/install` route
* `/auth` route
* Token exchange
* Token storage

📌 You must understand **every query param**.

#### 11. Webhooks

Learn:

* Why webhooks exist
* How to verify them
* GDPR webhooks
* App uninstall cleanup



## PHASE 6 — Embedded App Architecture 🧱

> This is Shopify-specific magic — and you’ll demystify it.

#### 12. Embedded Apps Explained

Understand:

* Iframes
* App Bridge
* Session tokens (JWT)
* Why redirects are special

🔧 Practice:

* Load app inside admin
* Authenticate frontend requests



## PHASE 7 — Frontend Layer (Admin UI)

#### 13. Admin UI Options

Learn:

* Plain HTML
* React
* Shopify Polaris

📌 Polaris is optional until later.



## PHASE 8 — Production Readiness 🚀

#### 14. Real-World App Concerns

Learn:

* Rate limiting
* Error retries
* Logging
* Env separation (dev / prod)
* HTTPS
* Secrets management



## PHASE 9 — Shopify CLI (Finally 😄)

> At this stage, CLI is a **tool**, not a crutch.

#### 15. CLI Boilerplate — Explained File by File

You will:

* Recognize OAuth middleware
* Understand session storage
* Know webhook handlers
* Modify confidently



## PHASE 10 — Advanced Mastery 🧠

#### 16. Advanced Topics

* Billing API
* App subscriptions
* Background jobs
* Multi-tenant scaling
* Shopify app performance
* App Store approval optimization
