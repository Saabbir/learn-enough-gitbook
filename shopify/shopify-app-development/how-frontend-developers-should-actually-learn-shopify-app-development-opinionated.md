# How Frontend Developers Should Actually Learn Shopify App Development (Opinionated)

If you're a front-end developer and want to be a **real Shopify app developer** (not just someone who pastes boilerplate), then **slowing down first** and learning what’s under the hood is the smartest move.

Below is a **clear, staged learning roadmap** designed specifically for **a front-end developer like you** who wants to master Shopify app development **from first principles**, file by file, line by line.



## How Shopify Apps Actually Work (Big Picture First)

![Image](https://cdn.prod.website-files.com/601411ff11c3efbd2603b7c9/62e3e6b65fb05758f79705da_DS_architecture.png.001-min.png)

![Image](https://cdn.shopify.com/shopifycloud/shopify-dev/production/assets/assets/images/apps/oauth-BVV3KNFj.png)

![Image](https://cdn.shopify.com/s/files/1/0533/2089/files/embedded-apps-new-vs-old-loading-flow.jpg?v=1601382242)

![Image](https://miro.medium.com/1*5QkBROnl4ExrOltdfJfteQ.png)

At its core, a Shopify app is:

```
Browser (Merchant)
   ↓
Your Frontend (React / HTML)
   ↓
Your Backend (Node / PHP / Ruby)
   ↓
Shopify APIs (Admin / Storefront / Webhooks)
```

**Key reality check:**\
👉 **Shopify apps are backend-heavy**, even if they look frontend-focused.



## Stage 0 – Mental Model You Must Lock In 🔒

Before touching Shopify at all, understand this:

| Concept        | Why it matters                                  |
| -------------- | ----------------------------------------------- |
| Backend server | Shopify talks to your server, not your frontend |
| OAuth          | How Shopify securely installs your app          |
| Access tokens  | How your app talks to Shopify APIs              |
| Database       | Where you store shop + token                    |
| Webhooks       | How Shopify notifies your app                   |
| Sessions       | How embedded apps stay authenticated            |

If you skip these → boilerplate will feel like magic\
If you master these → boilerplate becomes optional



## Stage 1 – Backend Fundamentals (Non-Negotiable)

You **must** learn backend basics first.\
No Shopify yet.

#### 1️⃣ Language Choice (Pick ONE)

For you, **Node.js** is the best fit.

* Same language as frontend
* Shopify officially supports it
* Huge ecosystem

👉 Focus on:

* JavaScript **runtime differences** (browser vs Node)
* Async / await deeply

**Core topics:**

* Event loop
* Non-blocking I/O
* Environment variables

#### 2️⃣ HTTP & Servers (CRITICAL)

Learn what’s really happening when you visit a URL.

You should understand:

* HTTP methods (`GET`, `POST`)
* Headers
* Status codes
* Cookies
* JSON bodies

Then learn **Express**:

```js
app.get('/install', (req, res) => {
  res.send('Hello Shopify');
});
```

Know exactly:

* What `req` is
* What `res` is
* How routing works

#### 3️⃣ Authentication & OAuth (This is Shopify’s heart ❤️)

Before Shopify:

* Learn OAuth **in general**

You must understand:

* Client ID
* Client Secret
* Redirect URI
* Authorization Code
* Access Token

👉 Shopify uses **OAuth 2.0**

If OAuth is fuzzy → Shopify apps will always feel confusing.

#### 4️⃣ Sessions, Cookies & Security

You need to know:

* What a session is
* Cookies vs JWT
* HTTP-only cookies
* CSRF
* Why tokens should **never** be in frontend JS

This explains:

* Why Shopify apps need a backend
* Why tokens are stored server-side



## Stage 2 – Databases & Persistence 🧠

Shopify apps are **stateful**.

You need to store:

* Shop domain
* Access token
* App metadata
* Sometimes merchant settings

#### What to learn:

* SQL vs NoSQL (either is fine)
* Tables / collections
* CRUD operations

Recommended starter:

* SQLite → PostgreSQL
* OR MongoDB (common in Shopify boilerplates)

Understand:

```js
shop: "example.myshopify.com"
accessToken: "shpat_xxx"
```



## Stage 3 – Shopify Fundamentals (Now You’re Ready)

Now we finally talk Shopify.

#### 1️⃣ Shopify Platform Basics

Learn these concepts **before coding**:

* Admin API
* Storefront API
* Webhooks
* Embedded apps
* App Bridge
* Polaris

Important APIs:

* REST vs GraphQL (Shopify prefers GraphQL now)

#### 2️⃣ Shopify OAuth (Connects Everything)

Flow you must be able to explain without notes:

1. Merchant clicks **Install App**
2. Shopify redirects to `/auth`
3. App redirects merchant to Shopify permission screen
4. Shopify sends `code`
5. App exchanges code → access token
6. App stores token in DB
7. App is installed 🎉

If you can’t draw this on paper → don’t move forward yet.



## Stage 4 – Build a Shopify App WITHOUT CLI 🚫

This is where you become dangerous (in a good way).

#### Build this manually:

**Backend**

* Node.js
* Express
* OAuth routes
* Token storage
* Admin API calls

**Frontend**

* Plain HTML or simple React
* No Polaris at first

You should be able to:

* Install app
* Fetch products
* Create a product
* Register a webhook



## Stage 5 – Embedded Apps & Frontend Layer

Now introduce:

* Embedded app concept
* App Bridge
* Iframes
* Session tokens (JWT)

Understand:

* Why Shopify apps run inside admin
* Why redirects behave differently
* Why session tokens exist



## Stage 6 – Webhooks & Production Readiness

Learn:

* Webhook verification (HMAC)
* GDPR webhooks
* App uninstall cleanup
* Rate limits
* Error handling



## Stage 7 – NOW Use Shopify CLI ✅

At this point:

* CLI is just a **productivity tool**
* You understand every generated file
* You know why it exists

You’ll recognize:

* OAuth middleware
* Session storage
* Webhook handlers
* API clients



## Technologies You’ll End Up Knowing

| Area         | Tech                      |
| ------------ | ------------------------- |
| Backend      | Node.js, Express          |
| Auth         | OAuth 2.0                 |
| DB           | PostgreSQL / MongoDB      |
| Shopify APIs | Admin API (GraphQL)       |
| Frontend     | React (optional), Polaris |
| Security     | HMAC, JWT, cookies        |
| Infra        | Env vars, HTTPS           |



## Honest Timeline ⏳

If done **properly**:

* Backend fundamentals: **3–4 weeks**
* OAuth + DB + security: **2–3 weeks**
* Shopify core concepts: **2 weeks**
* Manual Shopify app: **2–3 weeks**

👉 **2–3 months to solid competence**\
👉 **6 months to confidence**\
👉 **1 year to expert**



You’re approaching this like an engineer, not a tutorial follower — that’s how experts are made 💪
