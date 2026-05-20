# 📘 Article 6 — Talking to the Database (Your First SQL)

Perfect — now we’re in the **real learning zone**.\
You’ve got the environment, the mental model, and the calm. Let’s talk to the database properly.



## Think of SQL as a conversation

Before syntax, let’s lock in the right mindset.

When you write SQL, you are **not** telling the database _how_ to do something step by step.

You are telling it:

> **What data you want.**

The database handles the rest.

This is different from JavaScript or other programming languages — and once this clicks, SQL becomes much easier.



## The most important SQL word

The most important word in SQL is:

```sql
SELECT
```

If SQL were a spoken language, `SELECT` would mean:

> “Show me…”

Everything we do in this article builds around that idea.



## Start SQLite again

If it’s not already open, go back to your practice folder and run:

```bash
sqlite3 practice.db
```

You should see:

```
sqlite>
```

If you created the `users` table earlier, it’s still there.

Check:

```sql
.tables
```

You should see:

```
users
```



## SELECT everything (the simplest query)

Let’s start simple:

```sql
SELECT * FROM users;
```

Read this in plain English:

> “Show me everything from the users table.”

The `*` means:

> “All columns.”

You should see something like:

```
1|Alex|alex@example.com
```

That’s one row — one user.



## What just happened (slow explanation)

This query has three parts:

```sql
SELECT * FROM users
```

* `SELECT` → show me
* `*` → all columns
* `FROM users` → from the users table

That’s it.

No magic.



## Selecting specific columns

You don’t always need everything.

Try this:

```sql
SELECT name, email FROM users;
```

Now you’re saying:

> “Show me only the name and email of users.”

This is important later for:

* performance
* clarity
* avoiding unnecessary data



## SQL reads top to bottom (but thinks all at once)

This is subtle but important.

Even though SQL is written line by line, it is **not procedural**.

You are not saying:

1. get users
2. then filter
3. then output

You are describing a **result set**.

This mindset will save you a lot of confusion later.



## Formatting doesn’t matter (but readability does)

These two queries are identical:

```sql
SELECT * FROM users;
```

```sql
select
  *
from
  users;
```

SQL doesn’t care about:

* whitespace
* line breaks
* capitalization

Humans do.

We’ll use formatting that’s easy to read.



## Try adding another user

Let’s make things more interesting.

```sql
INSERT INTO users (name, email)
VALUES ('Sam', 'sam@example.com');
```

Now run:

```sql
SELECT * FROM users;
```

You should see two rows:

```
1|Alex|alex@example.com
2|Sam|sam@example.com
```

Notice:

* IDs are auto-incrementing
* order reflects insertion (for now — don’t rely on it)



## A very important rule (remember this later)

> **SQL does not guarantee order unless you ask for it.**

Right now the rows appear ordered by ID, but that’s incidental.

We’ll learn how to control order later.



## Reading SQL like a sentence

Train yourself to read SQL like this:

```sql
SELECT name, email
FROM users;
```

Becomes:

> “Show me the name and email from users.”

This habit makes complex queries much easier later.



## What you should practice right now

Before moving on, try these on your own:

* `SELECT * FROM users;`
* `SELECT name FROM users;`
* `SELECT email FROM users;`
* Add one more user and select again

Don’t rush.

Confidence comes from repetition.



## What you should take away from this article

Pause here and notice:

* SQL feels calm, not scary
* You can already read basic queries
* You are controlling _what_ data appears
* The database is doing the hard work

This is the foundation of everything else.



## What’s next

Now that you can **read data**, the next question is:

> “How does data get into the database in the first place?”

We’ve already used `INSERT`, but next we’ll slow it down and understand it properly.



## Up next

➡ **Article 7 — Adding New Data**
