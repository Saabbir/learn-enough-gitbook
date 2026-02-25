# 2️⃣ OAuth Hands-On Part 2

## Project Structure & App Architecture (Refactor Properly)

### Goal of this article

By the end, you will:

* Create proper **route modules**
* Keep `app.js` as a clean bootstrap file
* Understand **route mounting**
* End up with a scalable Shopify app structure

No new Shopify concepts — just **professional architecture**.



## Where We Are Starting From

Right now you have:

```txt
app.js          ← too much responsibility
db/
routes/         ← empty
services/
utils/
.env
shops.db
```

Our goal is:

```txt
app.js          ← bootstrap only
routes/
  install.js    ← /install
  auth.js       ← /auth/callback
db/
utils/
services/       ← coming next
```



## Rule of Thumb (Very Important)

> **app.js should never contain business logic**

It should only:

* Configure middleware
* Mount routes
* Start the server

Everything else goes elsewhere.



## ✅ STEP 1 — Create `/routes/install.js`

Open `routes/install.js` and paste **exactly this**:

```js
const express = require('express');
const { isValidShop, generateState } = require('../utils/shopify');

const router = express.Router();

router.get('/install', (req, res) => {
  const { shop } = req.query;

  // 1. Validate shop
  if (!shop || !isValidShop(shop)) {
    return res.status(400).send('Invalid shop parameter');
  }

  // 2. Generate state
  const state = generateState();

  // 3. Store state in cookie
  res.cookie('shopify_state', state, {
    httpOnly: true,
    secure: true,
    sameSite: 'lax'
  });

  // 4. Build redirect URL
  const redirectUri = `${process.env.APP_URL}/auth/callback`;

  const installUrl =
    `https://${shop}/admin/oauth/authorize` +
    `?client_id=${process.env.SHOPIFY_API_KEY}` +
    `&scope=${process.env.SHOPIFY_SCOPES}` +
    `&redirect_uri=${encodeURIComponent(redirectUri)}` +
    `&state=${state}`;

  // 5. Redirect to Shopify
  res.redirect(installUrl);
});

module.exports = router;
```

#### Key change

* We are using `router`, not `app`
* This file handles **only** `/install`



## ✅ STEP 2 — Create `/routes/auth.js`

Open `routes/auth.js` and paste:

```js
const express = require('express');
const fetch = require('node-fetch');
const verifyHmac = require('../utils/verifyHmac');
const db = require('../db/database');

const router = express.Router();

router.get('/auth/callback', async (req, res) => {
  const { shop, code, state } = req.query;
  const storedState = req.cookies.shopify_state;

  // 1. Verify state
  if (!state || state !== storedState) {
    return res.status(403).send('Invalid state');
  }

  // 2. Verify HMAC
  const isValid = verifyHmac(req.query, process.env.SHOPIFY_API_SECRET);
  if (!isValid) {
    return res.status(400).send('HMAC validation failed');
  }

  // 3. Exchange code for access token
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

  if (!accessToken) {
    return res.status(500).send('Failed to get access token');
  }

  // 4. Save token to DB
  const sql = `
    INSERT INTO shops (shop_domain, access_token)
    VALUES (?, ?)
    ON CONFLICT(shop_domain)
    DO UPDATE SET access_token=excluded.access_token
  `;

  db.run(sql, [shop, accessToken]);

  // 5. Cleanup
  res.clearCookie('shopify_state');

  // 6. Finish
  res.send('🎉 App installed successfully');
});

module.exports = router;
```

### What We Just Achieved

* `/install` logic is isolated
* `/auth/callback` logic is isolated
* OAuth is now **modular**
* These files can grow without chaos



## ✅ STEP 3 — Clean Up `app.js` (Very Important)

Now replace **entire `app.js`** with this:

```js
require('dotenv').config();

const express = require('express');
const cookieParser = require('cookie-parser');
require('./db/database'); // initialize DB

const installRoutes = require('./routes/install');
const authRoutes = require('./routes/auth');

const app = express();

app.use(express.json());
app.use(cookieParser());

// Health check
app.get('/', (req, res) => {
  res.send('Shopify app server running');
});

// Mount routes
app.use(installRoutes);
app.use(authRoutes);

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

### Read This Carefully

#### `require('./db/database')`

* Initializes DB
* Creates tables
* One-time side effect
* No variable needed

This is a **common backend pattern**.

#### `app.use(installRoutes)`

This tells Express:

> “Any route defined in `install.js` is now active.”

Same for `authRoutes`.

This is called **route mounting**.



## ✅ STEP 4 — Restart Server & Verify

Stop server:

```bash
CTRL + C
```

Start again:

```bash
node app.js
```

You should see:

```txt
SQLite connected
Server running on http://localhost:3000
```

Test:

```
http://localhost:3000/
```

Expected:

```
Shopify app server running
```



## Optional Test (If You Have Tunnel Ready)

```
/install?shop=your-store.myshopify.com
```

Redirect should still work exactly the same.



## Why This Structure Matters (Big Picture)

You now have:

| File              | Responsibility   |
| ----------------- | ---------------- |
| app.js            | App bootstrap    |
| routes/install.js | OAuth start      |
| routes/auth.js    | OAuth completion |
| utils             | Pure helpers     |
| db                | Persistence      |

This is:

* Scalable
* Readable
* Reviewable
* Production-grade

This is **how real Shopify apps are structured**.



### You Just Crossed a Line

At this point:

* You are no longer “following a tutorial”
* You are structuring a backend like a professional
* You can explain _why_ each file exists

That’s huge.



## ➡️ Next Article — Setting up Tunnel (ngrok / Cloudflare)
