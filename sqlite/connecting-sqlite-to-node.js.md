# Connecting SQLite to Node.js

This is where things _merge_ — JavaScript + database = **real backend power**.

### Goal of this article

By the end, you will:

* Connect Node.js to a SQLite `.db` file
* Run SQL queries **from JavaScript**
* Insert data into SQLite using Node
* Read data back into Node
* Understand the async nature of database calls
* Be fully ready to store Shopify OAuth data



### 📦 Step 1 — Install SQLite Package for Node

We’ll use the official, simple package:

```bash
npm install sqlite3
```

This lets Node:

* Open `.db` files
* Run SQL queries
* Read/write data

Check `package.json`:

```json
"dependencies": {
  "express": "^4.18.0",
  "sqlite3": "^5.x.x"
}
```



### 🧱 Step 2 — Project Structure (Clean & Intentional)

Let’s organize things properly.

```txt
shopify-app/
├── app.js
├── db/
│   └── database.js
├── shops.db
├── package.json
└── node_modules/
```

Why this matters:

* `db/` → database-related logic
* Separation of concerns (important habit)



### ✍️ Step 3 — Create Database Connection File

Create:

```txt
db/database.js
```

Add this code:

```js
const sqlite3 = require('sqlite3').verbose();
const path = require('path');
```

#### What’s happening:

* `sqlite3` → database library
* `verbose()` → better error messages
* `path` → safely build file paths

#### Open the database file

Add below:

```js
const dbPath = path.join(__dirname, '../shops.db');

const db = new sqlite3.Database(dbPath, (err) => {
  if (err) {
    console.error('Failed to connect to database', err);
  } else {
    console.log('Connected to SQLite database');
  }
});

module.exports = db;
```

#### Read this slowly

* `shops.db` → same file you created earlier
* If file exists → SQLite opens it
* If not → SQLite creates it
* `db` → connection object you reuse everywhere

This file runs **once** when imported.



### 🧠 Important Insight

> Opening the database does **not** read all data into memory.

It just:

* Opens a connection
* Allows queries when needed



### 🔌 Step 4 — Use the Database in `app.js`

Open `app.js` and import the DB:

```js
const express = require('express');
const db = require('./db/database');

const app = express();
```

When you run:

```bash
node app.js
```

You should see:

```txt
Connected to SQLite database
Server running on http://localhost:3000
```

That means:\
✅ Node → SQLite connection works



### 🧱 Step 5 — Create Table from Node (Important)

We **must not** rely on manual SQLite setup.

Add this **once** in `database.js`:

```js
db.run(`
  CREATE TABLE IF NOT EXISTS shops (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    shop_domain TEXT UNIQUE,
    access_token TEXT,
    installed_at DATETIME DEFAULT CURRENT_TIMESTAMP
  )
`);
```

Why this matters:

* App can start on a fresh machine
* Table auto-creates if missing
* This is **production thinking**



### 🧠 `IF NOT EXISTS` = Safe Startup

* Table exists → nothing happens
* Table missing → created

No crashes.



### ✍️ Step 6 — Insert Data from Node

Add a route in `app.js`:

```js
app.get('/add-shop', (req, res) => {
  const shop = 'demo-store.myshopify.com';
  const token = 'shpat_demo_123';

  const sql = `
    INSERT INTO shops (shop_domain, access_token)
    VALUES (?, ?)
  `;

  db.run(sql, [shop, token], function (err) {
    if (err) {
      return res.status(500).send('Database error');
    }
    res.send('Shop saved with ID ' + this.lastID);
  });
});
```

#### What’s happening here

* `?` → placeholders (prevents SQL injection)
* Array values → safely inserted
* `this.lastID` → ID of inserted row
* Async callback → runs when DB finishes



### 🌍 Test It

1. Restart server
2. Visit:

```
http://localhost:3000/add-shop
```

You should see:

```
Shop saved with ID 1
```

🎉 Node just wrote to SQLite.



### 🔍 Step 7 — Read Data from SQLite

Add another route:

```js
app.get('/shops', (req, res) => {
  db.all('SELECT * FROM shops', (err, rows) => {
    if (err) {
      return res.status(500).json({ error: 'Database error' });
    }
    res.json(rows);
  });
});
```

Visit:

```
http://localhost:3000/shops
```

You should see JSON like:

```json
[
  {
    "id": 1,
    "shop_domain": "demo-store.myshopify.com",
    "access_token": "shpat_demo_123",
    "installed_at": "2026-01-30 12:34:56"
  }
]
```

This is **real backend data flow**.



### 🧠 Async Reality (Very Important)

Database calls are **asynchronous**.

That means:

* Node does not wait
* Callback runs later
* You must respond **inside** the callback

❌ Wrong:

```js
const rows = db.all(...);
res.json(rows); // rows is undefined
```

✅ Correct:

```js
db.all(..., (err, rows) => {
  res.json(rows);
});
```



### 🛒 How This Fits Shopify OAuth (Preview)

Soon, this exact logic will store:

```txt
shop_domain = my-store.myshopify.com
access_token = shpat_xxx
```

Every Shopify app **must** do this.

You are now technically capable of:

* Building OAuth storage
* Building production apps



### 🧠 Lock This Sentence In

Repeat this:

> My backend can now remember things forever.

That’s the jump from toy → real app.



### ✅ What You Should Understand Now

You should now be comfortable with:

* Connecting Node to SQLite
* Creating tables programmatically
* Inserting data from routes
* Reading data as JSON
* Handling async DB operations

This is **core backend skill**.
