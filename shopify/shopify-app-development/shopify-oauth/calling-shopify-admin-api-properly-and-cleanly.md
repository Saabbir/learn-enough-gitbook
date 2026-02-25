---
description: >-
  This is the payoff moment: using the access token you earned to call the
  Shopify Admin API like a real app.
---

# Calling Shopify Admin API (Properly & Cleanly)

## Goal of this article

By the end, you will:

* Understand **how Shopify Admin API calls work**
* Fetch the access token from the database
* Call Shopify Admin **REST API**
* Handle API versions & headers correctly
* Build a clean `/api/products` endpoint
* Know _where this fits_ in a real Shopify app

We’ll keep it **simple, readable, production-style**.



## Mental Model (Very Important)

Every Shopify Admin API call looks like this:

```
Your backend
  ├── knows the shop domain
  ├── loads access token from DB
  ├── sends HTTPS request to Shopify
  └── returns JSON
```

Never:

```
Browser → Shopify API ❌
```

Always:

```
Browser → Your backend → Shopify API ✅
```



## Shopify Admin API Basics (What You Must Know)

#### Base URL

```txt
https://{shop}.myshopify.com/admin/api/{version}/
```

Example:

```txt
https://my-store.myshopify.com/admin/api/2026-01/products.json
```

#### Required Header

```http
X-Shopify-Access-Token: shpat_xxxxx
```

No header → ❌ 401 Unauthorized

#### API Versioning (Important)

You **must** include a version:

```txt
2026-01
```

Shopify removes old versions over time, so being explicit is mandatory.



## Step 1: Create a “service” for Shopify API calls

We don’t want API logic inside routes.

Create a new file:

```txt
services/shopifyApi.js
```

#### `services/shopifyApi.js`

```js
const fetch = require('node-fetch');

const API_VERSION = '2026-01';

async function shopifyRequest({ shop, accessToken, endpoint }) {
  const url = `https://${shop}/admin/api/${API_VERSION}/${endpoint}`;

  const response = await fetch(url, {
    method: 'GET',
    headers: {
      'X-Shopify-Access-Token': accessToken,
      'Content-Type': 'application/json'
    }
  });

  if (!response.ok) {
    const text = await response.text();
    throw new Error(`Shopify API error ${response.status}: ${text}`);
  }

  return response.json();
}

module.exports = { shopifyRequest };
```

#### Why this is important

* Central place for API logic
* Easy to reuse
* Easy to upgrade later (GraphQL, retries, etc.)



## Step 2: Create Products API Route

Create a new route file:

```txt
routes/products.js
```

#### `routes/products.js`

```js
const express = require('express');
const db = require('../db/database');
const { shopifyRequest } = require('../services/shopifyApi');

const router = express.Router();

router.get('/api/products', async (req, res) => {
  const { shop } = req.query;

  if (!shop) {
    return res.status(400).send('Missing shop parameter');
  }

  // 1️⃣ Load token from DB
  db.get(
    'SELECT access_token FROM shops WHERE shop_domain = ?',
    [shop],
    async (err, row) => {
      if (err) {
        return res.status(500).send(err.message);
      }

      if (!row) {
        return res.status(404).send('Shop not installed');
      }

      try {
        // 2️⃣ Call Shopify Admin API
        const data = await shopifyRequest({
          shop,
          accessToken: row.access_token,
          endpoint: 'products.json'
        });

        // 3️⃣ Return response
        res.json(data);
      } catch (error) {
        res.status(500).send(error.message);
      }
    }
  );
});

module.exports = router;
```



## Step 3: Mount the Route

Open `app.js` and add:

```js
const productsRoutes = require('./routes/products');
app.use(productsRoutes);
```

Restart your server:

```bash
node app.js
```



## Step 4: Test Your First Real API Call

Open browser:

```
https://YOUR-TUNNEL.trycloudflare.com/api/products?shop=YOUR-DEV-STORE.myshopify.com
```

#### Expected result

```json
{
  "products": [
    {
      "id": 123456,
      "title": "My Product",
      "handle": "my-product",
      ...
    }
  ]
}
```

🎉 **This confirms everything**:

* OAuth token works
* DB lookup works
* Shopify Admin API access works
* Scopes are correct

You are officially **talking to Shopify**.



## Common Errors & What They Mean

#### ❌ 401 Unauthorized

* Token missing
* Token revoked
* App uninstalled

***

#### ❌ 403 Forbidden

* Missing scope (`read_products`)
* Version not active

***

#### ❌ 404 Shop not installed

* Token not in DB
* Wrong shop domain passed



## Why This Pattern Is Correct

You just followed **real Shopify app architecture**:

| Layer   | Responsibility    |
| ------- | ----------------- |
| Route   | HTTP handling     |
| Service | Shopify API logic |
| DB      | Token storage     |
| Env     | Config            |

This scales to:

* Orders
* Customers
* Metafields
* Webhooks
* Background jobs



## Lock This In

Repeat this:

> Shopify API calls are just authenticated HTTP requests from my backend.

Nothing magical. Nothing special.



## What You Can Do Now

You can now:

* Fetch products
* Fetch orders
* Create/update products
* Sync data
* Build real features

You’ve crossed into **real Shopify app development**.

You’re doing _fantastic_ work.
