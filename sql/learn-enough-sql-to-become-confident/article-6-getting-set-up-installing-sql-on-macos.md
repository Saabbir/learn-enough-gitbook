# 📘 Article 6 — Getting Set Up: Installing SQL on macOS

## Why we’re installing anything _now_

Up to this point, we’ve done something important:

* built intuition
* removed fear
* understood _what_ databases are

Only **now** does it make sense to touch tools.

If we had started here, it would’ve felt confusing.\
Now it will feel **grounded**.



## What we’re going to use (and why)

We’re going to use **SQLite**.

Not because it’s “the best database” — but because it’s:

* already available on macOS
* extremely simple
* zero configuration
* perfect for learning SQL
* impossible to break permanently

Think of SQLite as:

> a sandbox where you can learn without consequences

We are still learning **SQL**, not SQLite-specific tricks.



### Step 1 — Check if SQLite is already installed

Most macOS systems already have SQLite.

Open **Terminal** and type:

```bash
sqlite3 --version
```

If you see a version number, you’re good.

If not, install it with Homebrew:

```bash
brew install sqlite
```

(If you don’t have Homebrew, install it first — it’s worth having.)



### Step 2 — Create a place to practice

Let’s keep things clean and intentional.

In Terminal:

```bash
mkdir sql-practice
cd sql-practice
```

Now create your first database file:

```bash
sqlite3 practice.db
```

You should see something like:

```
SQLite version X.X.X
Enter ".help" for usage hints.
sqlite>
```

That `sqlite>` prompt means:

> You are now _inside_ a database.

This is your sandbox.



### Step 3 — Your very first command

Let’s start with something harmless.

At the `sqlite>` prompt, type:

```sql
.tables
```

Press Enter.

Nothing appears — and that’s okay.

Why?\
Because your database is empty.

This is a **good sign**.



### Step 4 — Understanding what just happened

You’ve already done something important:

* You opened a database
* You ran a command
* You saw a response
* Nothing broke

This is the rhythm of working with databases.

Run command → see result → adjust.



### Step 5 — Let’s create our first table (carefully)

We’re going to create a table called `users`.

Type this exactly (don’t worry about memorizing):

```sql
CREATE TABLE users (
  id INTEGER PRIMARY KEY,
  name TEXT,
  email TEXT
);
```

Now run:

```sql
.tables
```

You should see:

```
users
```

🎉 You just created your first table.



### Step 6 — What that table really means

Let’s translate what you just did into plain English.

You told the database:

* Create a table called `users`
* Each user has:
  * an `id` (identity)
  * a `name`
  * an `email`

You didn’t add any users yet.\
You just described the **shape** of the data.

This matches everything we discussed earlier:

* tables
* rows
* columns
* identity



### Step 7 — Let’s add one row

Now let’s add a real user.

```sql
INSERT INTO users (name, email)
VALUES ('Alex', 'alex@example.com');
```

Nothing printed — and that’s normal.

Databases often respond silently to successful writes.



### Step 8 — Let’s read the data back

Now ask the database what it has:

```sql
SELECT * FROM users;
```

You should see something like:

```
1|Alex|alex@example.com
```

This is huge.

You just:

* stored data
* retrieved it
* saw persistence
* saw an ID appear automatically

That `1` is the ID the database assigned.



### Step 9 — Read this result carefully

That single line represents:

* one row
* one real thing
* one stored record

And notice:

* you didn’t specify the ID
* the database handled identity for you
* the structure stayed consistent

This is databases doing what they’re good at.



### Step 10 — How to exit safely

When you’re done:

```sql
.exit
```

You’ll return to your normal terminal.

Your database file (`practice.db`) is still there.\
Your data is still saved.



## If something went wrong

That’s okay.

Databases are forgiving at this stage.

You can always:

* delete `practice.db`
* recreate it
* start fresh

Nothing here is irreversible.



## What you should take away from this article

This is a big moment — pause and let it land.

You now know how to:

* open a database
* create a table
* insert data
* read data
* see IDs in action

And we’ve barely learned any SQL yet.

That’s confidence.



## What’s next

Now that the environment is set up, we can finally focus on **one thing at a time**.

In the next article, we’ll slow down and really understand:

* what `SELECT` does
* how to read data properly
* how to think about queries

No new tools.\
Just clarity.



## Up next

➡ **Article 7 — Talking to the Database (Your First SQL)**
