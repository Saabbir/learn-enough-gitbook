# 📘 Implementing /install Route (Node + Express)

## Goal of this article

By the end, you will:

* Implement `/install` **fully**
* Validate the `shop` parameter safely
* Generate and store `state`
* Build the Shopify OAuth URL **by hand**
* Redirect correctly
* Understand **every single line**

After this, your app officially **starts OAuth**.



## What `/install` Is Responsible For (Final Reminder)

`/install` does **only this**:

> Take a `shop` → redirect to Shopify’s permission screen

No tokens.\
No database writes.\
No API calls.



## Step 0 — Environment Variables (Required)

Create a `.env` file in your project root:

```env
SHOPIFY_API_KEY=your_api_key_here
SHOPIFY_SCOPES=read_products,write_products
APP_URL=https://your-app-url.ngrok.app
```

⚠️ **Do not commit this file**

#### Load env vars in `app.js`

At the very top of `app.js`:

```js
require('dotenv').config();
```

Install dotenv if needed:

```bash
npm install dotenv
```

### Why This Matters

* API key → secret
* App URL → different per environment
* Scopes → configurable

This is **professional setup**, not tutorial glue.



## Step 1 — Add Helpers (Clean Code Habit)

Create a new folder:

```txt
utils/
  └── shopify.js
```

#### `utils/shopify.js`

```js
const crypto = require('crypto');

function isValidShop(shop) {
  return /^[a-zA-Z0-9][a-zA-Z0-9\-]*\.myshopify\.com$/.test(shop);
}

function generateState() {
  return crypto.randomBytes(16).toString('hex');
}

module.exports = {
  isValidShop,
  generateState
};
```

#### Why this matters

* Reusable
* Testable
* Keeps `app.js` readable



## Step 2 — Prepare Express (Middleware)

In `app.js`, make sure you have:

```js
const express = require('express');
const app = express();

app.use(express.json());
```



## Step 3 — Implement `/install` Route (Core)

Add this to `app.js`:

```js
const { isValidShop, generateState } = require('./utils/shopify');

app.get('/install', (req, res) => {
  const { shop } = req.query;

  // 1️⃣ Validate shop param
  if (!shop || !isValidShop(shop)) {
    return res.status(400).send('Invalid shop parameter');
  }

  // 2️⃣ Generate state
  const state = generateState();

  // 3️⃣ Store state temporarily (cookie for now)
  res.cookie('shopify_state', state, {
    httpOnly: true,
    secure: true,
    sameSite: 'lax'
  });

  // 4️⃣ Build Shopify OAuth URL
  const redirectUri = `${process.env.APP_URL}/auth/callback`;

  const installUrl =
    `https://${shop}/admin/oauth/authorize` +
    `?client_id=${process.env.SHOPIFY_API_KEY}` +
    `&scope=${process.env.SHOPIFY_SCOPES}` +
    `&redirect_uri=${encodeURIComponent(redirectUri)}` +
    `&state=${state}`;

  // 5️⃣ Redirect merchant to Shopify
  res.redirect(installUrl);
});
```

Now let’s break this **line by line**.



## Line-by-Line Explanation (Critical)

#### `req.query.shop`

Comes from:

```
/install?shop=my-store.myshopify.com
```

This is the **store identity**.

***

#### `isValidShop(shop)`

Prevents:

* Script injection
* Fake domains
* Broken OAuth flow

Never trust input. Ever.

***

#### `state = generateState()`

* Random
* Unique
* Hard to guess

This protects against CSRF.

***

#### `res.cookie(...)`

We temporarily store `state` in a cookie so we can **verify it later** in `/auth/callback`.

This is not optional.

***

#### `redirectUri`

Must:

* Be HTTPS
* Match exactly what you’ll configure in Shopify
* Point to `/auth/callback`

***

#### `encodeURIComponent`

Prevents:

* Broken URLs
* Subtle OAuth bugs

Never skip this.

***

#### `res.redirect(installUrl)`

This ends `/install`.

Your server:

* Does NOT continue
* Does NOT respond with HTML
* Hands control back to Shopify

***

### 🧪 Step 4 — Test `/install` Manually

Start your server:

```bash
node app.js
```

Open browser:

```
http://localhost:3000/install?shop=your-dev-store.myshopify.com
```

What should happen:

1. Browser redirects to Shopify
2. Shopify shows permission screen
3. You see requested scopes

🎉 OAuth has officially started.



## Common Beginner Mistakes (Avoid These)

❌ Calling Shopify API here\
❌ Writing to database\
❌ Skipping `state`\
❌ Not validating `shop`\
❌ Hardcoding URLs

You avoided all of them.

### Lock This Sentence In

Repeat this:

> `/install` only redirects. OAuth continues elsewhere.

If you remember that, you’ll never overcomplicate OAuth.



## ✅ What You Have Now

You now have:

* A valid `/install` route
* Secure `shop` validation
* Proper `state` handling
* Correct OAuth URL generation
* A real Shopify permission screen

You are officially **inside the Shopify OAuth flow**.



## ➡️ Next Article — Handling `/auth/callback` (HMAC, Code → Token)

Next we will:

* Receive Shopify’s callback
* Verify `hmac`
* Verify `state`
* Exchange `code` for **access token**
* Store token in SQLite
* Complete app installation

This is where your app becomes **installed for real**.
