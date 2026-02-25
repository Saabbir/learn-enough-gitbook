# Verify Access Token

## What “checking the access token” really means

You want to confirm **three things**:

1. Shopify actually issued a token
2. Your backend **stored it correctly**
3. That token **works with Shopify Admin API**

We’ll do all three.



## Method 1: Check the database directly (Fastest)

Since you’re using **SQLite**, this is the most direct check.

#### 1️⃣ Open SQLite CLI

From your project root:

```bash
sqlite3 shops.db
```

You should see:

```txt
sqlite>
```

***

#### 2️⃣ List tables (sanity check)

```sql
.tables
```

Expected:

```txt
shops
```

***

#### 3️⃣ View stored shops + token

```sql
SELECT shop_domain, access_token FROM shops;
```

You should see something like:

```txt
my-dev-store.myshopify.com|shpat_abc123xyz
```

✅ If you see `shpat_...` → **token exists**\
❌ If empty → OAuth callback didn’t store it (but yours worked)

***

#### 4️⃣ Exit SQLite

```sql
.exit
```



## Method 2: Log it once in code (Temporary, for learning)

Open `routes/auth.js`.

Find this line:

```js
const accessToken = tokenData.access_token;
```

Add **temporary logging** below it:

```js
console.log('ACCESS TOKEN FOR', shop, accessToken);
```

Restart server and reinstall app once.

You should see in terminal:

```txt
ACCESS TOKEN FOR my-dev-store.myshopify.com shpat_xxxxxxxxx
```

⚠️ **Important**

* Do this only for learning
* Remove logs afterward
* Never log tokens in real production



## Method 3: Create a debug route (Very useful)

This is a **safe dev-only trick** to prove everything works.

#### 1️⃣ Add this route (temporary)

In `app.js` or a new debug route file:

```js
app.get('/debug/token', (req, res) => {
  const { shop } = req.query;

  db.get(
    'SELECT access_token FROM shops WHERE shop_domain = ?',
    [shop],
    (err, row) => {
      if (err) return res.status(500).send(err.message);
      if (!row) return res.status(404).send('Shop not found');

      res.json({
        shop,
        token_exists: !!row.access_token,
        token_preview: row.access_token.slice(0, 10) + '...'
      });
    }
  );
});
```

Restart server.

#### 2️⃣ Test it in browser

Open:

```
https://YOUR-TUNNEL.trycloudflare.com/debug/token?shop=YOUR-STORE.myshopify.com
```

Expected response:

```json
{
  "shop": "my-dev-store.myshopify.com",
  "token_exists": true,
  "token_preview": "shpat_abc..."
}
```

✅ Token confirmed\
🔐 Only preview shown (good practice)



## Method 4: The REAL proof: call Shopify Admin API (Best)

This is the **ultimate confirmation**.

If this works → token is valid, scopes are correct, everything is wired.

We’ll do a **simple products fetch**.

#### 1️⃣ Add this route (this becomes real app logic)

Create `routes/products.js` (or inline for now):

```js
const express = require('express');
const fetch = require('node-fetch');
const db = require('../db/database');

const router = express.Router();

router.get('/api/products', async (req, res) => {
  const { shop } = req.query;

  db.get(
    'SELECT access_token FROM shops WHERE shop_domain = ?',
    [shop],
    async (err, row) => {
      if (err) return res.status(500).send(err.message);
      if (!row) return res.status(404).send('Shop not installed');

      const response = await fetch(
        `https://${shop}/admin/api/2026-01/products.json`,
        {
          headers: {
            'X-Shopify-Access-Token': row.access_token
          }
        }
      );

      const data = await response.json();
      res.json(data);
    }
  );
});

module.exports = router;
```

Mount it in `app.js`:

```js
const productsRoutes = require('./routes/products');
app.use(productsRoutes);
```

Restart server.

#### 2️⃣ Test API call

Open:

```
https://YOUR-TUNNEL.trycloudflare.com/api/products?shop=YOUR-STORE.myshopify.com
```

If you see JSON with products → 🎉🎉🎉

That means:

* Token is valid
* Scopes are correct
* Admin API works
* Your app is **fully functional**



## Which Method Should You Trust?

| Method         | Purpose                    |
| -------------- | -------------------------- |
| SQLite query   | Confirms storage           |
| Console log    | Confirms OAuth exchange    |
| Debug route    | Confirms backend wiring    |
| Admin API call | **Confirms real access** ✅ |

👉 **Method 4 is the real one**.



### 🔐 Important Security Reminder

Before continuing seriously:

* Remove token logs
* Remove debug routes
* Never expose tokens to frontend

We’ll formalize this in next articles.



## ▶️ Next Logical Step: Calling Shopify Admin API (Properly & Cleanly)

Now that token is confirmed, next article is **finally the fun part**:

We’ll:

* Refactor API calls into services
* Handle errors
* Handle API versions
* Prepare for webhooks

You’re officially past setup hell 🚀
