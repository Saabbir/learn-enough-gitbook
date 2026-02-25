# 1️⃣ OAuth Hands-On Part 1

### ✅ STEP 0 — Verify prerequisites

Open VS Code terminal and run:

```bash
node -v
npm -v
```

You should see versions.\
If yes → continue.

***

### ✅ STEP 1 — Initialize Node project

In your empty project folder:

```bash
npm init -y
```

You now have:

```txt
package.json
```

This officially makes this a Node backend project.

***

### ✅ STEP 2 — Install core dependencies

Run **this single command**:

```bash
npm install express sqlite3 dotenv cookie-parser node-fetch@2
```

What each one is for (just awareness):

| Package       | Purpose               |
| ------------- | --------------------- |
| express       | Server & routing      |
| sqlite3       | Database              |
| dotenv        | Environment variables |
| cookie-parser | Read OAuth state      |
| node-fetch    | Call Shopify APIs     |

You now have:

```txt
node_modules/
package-lock.json
```

***

### ✅ STEP 3 — Create the folder structure (IMPORTANT)

Create these folders **exactly**:

```txt
.
├── app.js
├── db/
├── routes/
├── services/
├── utils/
└── .env
```

👉 Do this manually in VS Code sidebar\
(or via terminal if you prefer)

```bash
mkdir db routes services utils
touch app.js .env
```

***

### ✅ STEP 4 — Add environment variables

Open `.env` and paste this **template**:

```env
PORT=3000

SHOPIFY_API_KEY=PASTE_YOUR_API_KEY
SHOPIFY_API_SECRET=PASTE_YOUR_API_SECRET
SHOPIFY_SCOPES=read_products,write_products

APP_URL=https://YOUR_TUNNEL_URL
```

⚠️ Important notes:

* Use your **Partner Dashboard** app credentials
* `APP_URL` will be updated later (ngrok/cloudflare)
* Do **NOT** commit `.env`

***

### ✅ STEP 5 — Create database file

Create an empty file:

```txt
shops.db
```

That’s it.\
SQLite will handle the rest.

***

### ✅ STEP 6 — Create database connection file

Create:

```txt
db/database.js
```

Paste this **as-is**:

```js
const sqlite3 = require('sqlite3').verbose();
const path = require('path');

const dbPath = path.join(__dirname, '../shops.db');

const db = new sqlite3.Database(dbPath, err => {
  if (err) {
    console.error('DB connection failed', err);
  } else {
    console.log('SQLite connected');
  }
});

db.run(`
  CREATE TABLE IF NOT EXISTS shops (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    shop_domain TEXT UNIQUE,
    access_token TEXT,
    installed_at DATETIME DEFAULT CURRENT_TIMESTAMP
  )
`);

module.exports = db;
```

✔ This auto-creates tables on startup\
✔ No manual SQLite shell needed

***

### ✅ STEP 7 — Create utility helpers

#### `utils/shopify.js`

```js
const crypto = require('crypto');

function isValidShop(shop) {
  return /^[a-zA-Z0-9][a-zA-Z0-9\-]*\.myshopify\.com$/.test(shop);
}

function generateState() {
  return crypto.randomBytes(16).toString('hex');
}

module.exports = { isValidShop, generateState };
```

***

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

***

### ✅ STEP 8 — Create routes folder files (empty for now)

Create these empty files:

```txt
routes/install.js
routes/auth.js
```

Leave them empty — we’ll fill them later.

***

### ✅ STEP 9 — Create minimal `app.js`

Paste this into `app.js`:

```js
require('dotenv').config();

const express = require('express');
const cookieParser = require('cookie-parser');
const db = require('./db/database');

const app = express();

app.use(express.json());
app.use(cookieParser());

// health check
app.get('/', (req, res) => {
  res.send('Shopify app server running');
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server listening on http://localhost:${PORT}`);
});
```

***

### ✅ STEP 10 — Run the server (first success)

In terminal:

```bash
node app.js
```

You should see:

```txt
SQLite connected
Server listening on http://localhost:3000
```

Open browser:

```
http://localhost:3000
```

You should see:

```
Shopify app server running
```

🎉 **Backend foundation is live**



## 🧠 What You Have Right Now

Without writing Shopify logic yet, you already have:

* ✅ Real Node server
* ✅ Express configured
* ✅ SQLite database wired
* ✅ OAuth helpers ready
* ✅ Clean folder structure
* ✅ Environment config
* ✅ Production-grade foundation



## ▶️ Next: Hands-On Part 2

In **Hands-On Part 2**, we will:

* Move `/install` into `routes/install.js`
* Move `/auth/callback` into `routes/auth.js`
* Clean `app.js` to pure bootstrap
* Introduce **route mounting**
* Make the app scalable and readable
