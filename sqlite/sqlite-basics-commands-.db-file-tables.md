# SQLite Basics (Commands, .db File, Tables)

## Goal of this article

By the end, you will:

* Understand what a **`.db` file** really is
* Open SQLite from the terminal
* Use essential SQLite commands:
  * `.tables`
  * `.schema`
  * `.exit`
* Create a table
* Insert and read real data
* Clearly see how data persists on disk



## 🧠 First: What Is SQLite, Really?

SQLite is:

* A **real SQL database**
* Stored as a **single file**
* No server to run
* No username/password
* No setup ceremony

> If the file exists → the database exists.

That simplicity is why we start here.



## 📦 Step 1 — Install SQLite

#### On macOS / Linux

Most systems already have it.

Check:

```bash
sqlite3 --version
```

If not installed:

```bash
brew install sqlite
```

#### On Windows

Download from:\
[https://www.sqlite.org/download.html](https://www.sqlite.org/download.html)\
(Install the **sqlite-tools** package)



## 🧱 Step 2 — Create a Database File

Inside your project folder:

```bash
sqlite3 shops.db
```

What just happened?

* If `shops.db` didn’t exist → it was created
* You are now **inside** the database shell

You should see:

```txt
sqlite>
```

That’s SQLite waiting for commands.

### 🧠 Important Mental Model

> The `.db` file **is** the database.

Delete the file → database gone\
Copy the file → database copied



## 📋 Step 3 — List Tables

Run:

```sql
.tables
```

Output:

```txt
(no tables)
```

That’s normal. Empty database.



## 🧱 Step 4 — Create Your First Table

Let’s create a table for Shopify shops.

```sql
CREATE TABLE shops (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  shop_domain TEXT NOT NULL,
  access_token TEXT NOT NULL,
  installed_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

Press Enter.

No output = success ✅

### 🧠 Read This Like English

* `shops` → table name
* `id` → unique identifier
* `TEXT` → string
* `NOT NULL` → required
* `DEFAULT` → auto-filled value

This is **schema**.



## 📋 Step 5 — Check Tables Again

```sql
.tables
```

Output:

```txt
shops
```

You just created **persistent structure**.



## 🧠 Step 6 — View Table Schema

```sql
.schema shops
```

You’ll see the exact SQL used to create it.

This answers:

> “What columns does this table have?”



## ✍️ Step 7 — Insert Data

Let’s store a fake Shopify store.

```sql
INSERT INTO shops (shop_domain, access_token)
VALUES ('test-store.myshopify.com', 'shpat_test_123');
```

Again:

* No output = success



## 🔍 Step 8 — Read Data (SELECT)

```sql
SELECT * FROM shops;
```

Output:

```txt
1|test-store.myshopify.com|shpat_test_123|2026-01-30 12:00:00
```

🎉 That data is now **on disk**.



## 🧠 Critical Moment — Restart Test



1. Exit SQLite:

```sql
.exit
```

2. Reopen it:

```bash
sqlite3 shops.db
```

3. Run:

```sql
SELECT * FROM shops;
```

The data is **still there**.

This is persistence.



## 🧠 SQLite Meta Commands (Dot Commands)

These are **SQLite-only**, not SQL.

#### `.tables`

Lists all tables

***

#### `.schema`

Shows structure

***

#### `.exit`

Exits SQLite

***

#### `.help`

Lists all available commands

***

### 🧠 SQL vs SQLite Commands (Important)

| Type         | Example                       |
| ------------ | ----------------------------- |
| SQL          | `SELECT`, `INSERT`, `CREATE`  |
| SQLite shell | `.tables`, `.schema`, `.exit` |

Dot commands work **only** in the SQLite shell.



## 🧠 One Mental Model to Lock It In

Repeat this:

> SQLite is just a smart file that understands SQL.

That’s it.



## 🧪 Common Beginner Questions (Answered)

#### “Is SQLite temporary?”

❌ No. It’s persistent.

***

#### “Can multiple apps read it?”

Yes — but carefully (later topic).

***

#### “Will this work in production?”

For small apps — yes.\
For large apps — migrate later.



## ✅ What You Should Understand Now

You should now be comfortable with:

* What a `.db` file is
* Opening SQLite
* Creating tables
* Inserting data
* Reading data
* Seeing persistence in action

This is **real backend data handling**.



## ➡️ Next Article — Connecting SQLite to Node.js

We’ll:

* Install SQLite Node package
* Open the `.db` file from Node
* Run SQL queries from JavaScript
* Insert & fetch data programmatically
* Prepare for Shopify OAuth storage

This is where **backend + database** finally connect.
