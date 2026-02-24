# 📘 Shopify App Architecture (OAuth Big Picture)

## Goal of this article

By the end, you will clearly understand:

* The **full Shopify OAuth flow**
* Why redirects exist
* What `shop`, `code`, `hmac`, and `state` are
* Which **routes your backend must have**
* Where your database fits in
* Why this architecture is secure

No code yet — **architecture first**.



## 🧠 First: Why OAuth Exists at All

Let’s start with the _problem_.

Shopify needs to ensure:

* Apps don’t steal data
* Merchants control access
* Tokens aren’t leaked
* Apps can be revoked instantly

So Shopify uses **OAuth 2.0**.

In plain English:

> OAuth lets a store say\
> “I trust this app — here’s limited access.”



## 🧱 Who Are the Players?

There are **three actors** in Shopify OAuth:

1. **Merchant (human)**
2. **Your App Server**
3. **Shopify**

Important:

* The browser is just a messenger
* All decisions happen server-to-server



## 🧭 The Full OAuth Flow (High Level)

Here’s the **entire journey**, end to end.

![Image](https://cdn.shopify.com/shopifycloud/shopify-dev/production/assets/assets/images/apps/oauth-BVV3KNFj.png)

![Image](https://cdn.prod.website-files.com/61a360375a0df47724a5f2cc/61e4c8204a0c7c1db8dcc543_0*K_OoDNmsV8-N_KER.png)

Let’s break this down **step by step**.



## 🔁 Step-by-Step OAuth Flow (Plain English)

#### ① Merchant clicks “Install app”

This happens inside the Shopify Admin.

Shopify now knows:

* Which app
* Which store

***

#### ② Shopify redirects to your app `/install`

URL example:

```
https://your-app.com/install?shop=my-store.myshopify.com
```

At this moment:

* Shopify has NOT given access
* Your app has NO token
* You only know the store domain

***

#### ③ Your app redirects back to Shopify (Permission screen)

Your server builds a URL like:

```
https://my-store.myshopify.com/admin/oauth/authorize
```

With parameters:

* `client_id`
* `scope`
* `redirect_uri`
* `state`

This tells Shopify:

> “Ask the merchant if this app can access these things.”

***

#### ④ Merchant approves permissions

Merchant sees:

* App name
* Requested permissions
* Approve / Cancel buttons

This is **explicit consent**.

***

#### ⑤ Shopify redirects to your callback URL

Example:

```
https://your-app.com/auth/callback
  ?shop=my-store.myshopify.com
  &code=abc123
  &hmac=xyz
  &state=nonce
```

This is the **most critical moment**.

***

#### ⑥ Your app verifies & exchanges code for token

Your backend:

1. Verifies `hmac` (security)
2. Exchanges `code` for an **access token**
3. Stores token in database

Now:\
✅ App is installed\
✅ App can call Shopify APIs

***

### 🧠 Let’s Explain Each Parameter (Very Important)

#### `shop`

Example:

```
my-store.myshopify.com
```

Meaning:

* Store identity
* Used as **primary key** in DB
* Used in every API call

***

#### `code`

* Temporary authorization code
* Single-use
* Short-lived

Think:

> “Proof that the merchant approved the app”

***

#### `hmac`

* Cryptographic signature
* Prevents tampering
* Ensures request is from Shopify

If this fails:\
❌ Reject request immediately

***

#### `state`

* Random value you generated
* Prevents CSRF attacks
* Must match what you sent earlier

Security without user awareness.



## 🧠 Why Redirects Are Required

Important realization:

> Shopify never talks directly to your database.

Everything happens via:

* Browser redirects
* Server-to-server API calls

Redirects allow:

* Merchant interaction
* Permission screens
* Secure handoff



## 🧱 Your Backend Routes (Clear Now)

After this article, these routes should make sense:

```txt
GET  /install
GET  /auth/callback
```

Later:

```txt
POST /webhooks/app/uninstalled
```

Each route has **one responsibility**.



## 🗄️ Where the Database Fits

Your database stores:

| Field         | Why             |
| ------------- | --------------- |
| shop\_domain  | Store identity  |
| access\_token | API access      |
| installed\_at | Audit / cleanup |

Once stored:

* You never run OAuth again
* Unless app is uninstalled



## 🔐 Why Access Tokens Are Server-Only

Access tokens:

* Grant full API access
* Must never reach browser
* Must never be exposed

That’s why:

```
Browser → Server → Shopify API
```

Never:

```
Browser → Shopify API
```



## 🧠 Mental Model (Lock This In)

Repeat this:

> OAuth is just a secure handshake\
> where Shopify gives my server permission.

If you remember that, OAuth stops feeling scary.



## 🧪 Common Beginner Confusions (Cleared)

#### “Why not store password?”

❌ Security nightmare\
❌ Merchants would never trust it

***

#### “Why so many redirects?”

Because:

* Humans approve
* Machines exchange tokens

***

#### “Why can’t frontend do this?”

Because:

* Secrets
* Security
* Verification



## ✅ What You Should Understand Now

You should now be clear on:

* Why OAuth exists
* The full install flow
* What each query param means
* Which backend routes are required
* Where tokens are stored
* Why backend is mandatory

This is **core Shopify app knowledge**.



## ➡️ Next Article — OAuth 2.0 Explained (From Zero, No Jargon)

Next we’ll:

* Explain OAuth **without Shopify**
* Use real-world analogies
* Remove all remaining confusion
* Prepare to write `/install` confidently
