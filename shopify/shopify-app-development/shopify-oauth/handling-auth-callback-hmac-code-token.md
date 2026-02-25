---
description: >-
  This is the most security-critical part of a Shopify app — we’ll go slow and
  explain every check.
---

# 📘 Handling /auth/callback (HMAC, Code → Token)

## Goal of this article

By the end, you will:

* Implement `/auth/callback` **line by line**
* Verify **state** (CSRF protection)
* Verify **HMAC** (Shopify authenticity)
* Exchange **code → access token**
* Store the token securely in SQLite
* Fully complete app installation

After this article, your app is **officially installed**.



## Where We Are in OAuth

So far, we did this:

```
Merchant → Shopify → /install → Shopify permission screen
```

Now we’re handling the **return trip**:

```
Shopify → /auth/callback → Your server
```

This is where Shopify says:

> “The merchant approved. Prove you’re legit, then I’ll give you a token.”



## What Shopify Sends to `/auth/callback`

Example callback URL:

```
GET /auth/callback
  ?shop=my-store.myshopify.com
  &code=abc123
  &hmac=xyz
  &state=nonce
  &timestamp=1700000000
```

These parameters are **everything**.



## What `/auth/callback` Must Do (Checklist)

Your route must:

* [ ] Verify `state` (CSRF protection)
* [ ] Verify `hmac` (request integrity)
* [ ] Exchange `code` for access token
* [ ] Store token in database
* [ ] Finish install (redirect / success page)

If **any check fails** → reject immediately.



## Step 0 — Required Packages

We’ll need `crypto` and `node-fetch`.

Install fetch:

```bash
npm install node-fetch@2
```

(We use v2 for CommonJS simplicity.)



## Step 1 — Add Cookie Middleware

We stored `state` in a cookie earlier, so we need to read it.

Install cookie parser:

```bash
npm install cookie-parser
```

In `app.js`:

```js
const cookieParser = require('cookie-parser');
app.use(cookieParser());
```

### Why This Matters

Without this:

* You can’t verify `state`
* OAuth is vulnerable to CSRF



## Step 2 — HMAC Verification Helper

Create a new helper file:

```txt
utils/verifyHmac.js
```

#### `utils/verifyHmac.js`

```js
const crypto = require('crypto');

function verifyHmac(query, secret) {
  const { hmac, ...rest } = query;

  const message = Object.keys(rest)
    .sort()
    .map(key => `${key}=${rest[key]}`)
    .join('&');

  const generatedHmac = crypto
    .createHmac('sha256', secret)
    .update(message)
    .digest('hex');

  return generatedHmac === hmac;
}

module.exports = verifyHmac;
```

### Read This Slowly

* Shopify signs query params with your **API secret**
* You rebuild the message
* You hash it
* You compare

If it matches → request is from Shopify\
If not → reject immediately



## Step 3 — Implement `/auth/callback`

Add this route to `app.js`:

```js
const fetch = require('node-fetch');
const verifyHmac = require('./utils/verifyHmac');
const db = require('./db/database');

app.get('/auth/callback', async (req, res) => {
  const { shop, code, hmac, state } = req.query;
  const storedState = req.cookies.shopify_state;

  // 1️⃣ Verify state (CSRF)
  if (!state || state !== storedState) {
    return res.status(403).send('Invalid state');
  }

  // 2️⃣ Verify HMAC (Shopify authenticity)
  const isValid = verifyHmac(req.query, process.env.SHOPIFY_API_SECRET);
  if (!isValid) {
    return res.status(400).send('HMAC validation failed');
  }

  // 3️⃣ Exchange code for access token
  const tokenResponse = await fetch(
    `https://${shop}/admin/oauth/access_token`,
    {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        client_id: process.env.SHOPIFY_API_KEY,
        client_secret: process.env.SHOPIFY_API_SECRET,
        code
      })
    }
  );

  const tokenData = await tokenResponse.json();
  const accessToken = tokenData.access_token;

  // 4️⃣ Store token in database
  const sql = `
    INSERT INTO shops (shop_domain, access_token)
    VALUES (?, ?)
    ON CONFLICT(shop_domain)
    DO UPDATE SET access_token=excluded.access_token
  `;

  db.run(sql, [shop, accessToken]);

  // 5️⃣ Clean up state cookie
  res.clearCookie('shopify_state');

  // 6️⃣ Finish installation
  res.send('🎉 App installed successfully!');
});
```

Now let’s explain **every section**.



## Step-by-Step Explanation

#### 1️⃣ Verify `state`

```js
state === storedState
```

Why?

* Prevents forged callbacks
* Ensures this callback belongs to _this_ install

If this fails → stop immediately.

***

#### 2️⃣ Verify `hmac`

This proves:

* Request was not modified
* Request is from Shopify
* Query params are trusted

This is **non-negotiable** in production apps.

***

#### 3️⃣ Exchange `code → token`

This is a **server-to-server** request.

Important:

* Happens only once
* Code becomes invalid afterward
* Token is long-lived

This is the moment Shopify finally says:

> “Here’s your key.”

***

#### 4️⃣ Store Token Safely

We store:

* `shop_domain`
* `access_token`

Using:

```sql
ON CONFLICT(shop_domain) DO UPDATE
```

This makes install **idempotent**.

***

#### 5️⃣ Clear State Cookie

Security cleanup:

* State should never be reused
* Prevents replay attacks

***

#### 6️⃣ Finish Install

For now:

```txt
App installed successfully
```

Later:

* Redirect to embedded app UI
* Or dashboard
* Or background-only logic



## Common Beginner Mistakes (You Avoided)

❌ Skipping HMAC\
❌ Trusting query params blindly\
❌ Storing token in frontend\
❌ Not validating state\
❌ Mixing `/install` and `/callback` logic

You did this the **correct professional way**.

### Lock This In (Very Important)

Repeat this:

> `/auth/callback` is where trust is verified and access is granted.

If you understand that, OAuth will never confuse you again.



## ✅ What You Have Now (Big Milestone)

You now have:

* A fully working Shopify OAuth flow
* Secure `/install`
* Secure `/auth/callback`
* Token storage in SQLite
* A real installed app

You are no longer “learning Shopify apps”.

👉 You are **building Shopify apps**.
