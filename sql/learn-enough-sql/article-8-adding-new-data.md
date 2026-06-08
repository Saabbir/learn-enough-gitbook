# 📘 Article 8 — Adding New Data

## Data doesn’t appear magically

So far, you’ve:

* created a table
* viewed data with `SELECT`
* seen rows appear

But databases do not invent data themselves.

Somewhere:

* a user signs up
* a product is created
* an order is placed
* a form is submitted

And the application needs to say:

> “Please store this permanently.”

That’s what `INSERT` does.



## `INSERT` means “create a new row”

This is the basic shape:

```sql
INSERT INTO table_name (columns)
VALUES (values);
```

Read it slowly:

> “Insert into this table, using these columns, these values.”

That’s all.



## Let’s use the `users` table again

Your table currently looks conceptually like this:

| id | name | email                                       |
| -- | ---- | ------------------------------------------- |
| 1  | Alex | [alex@example.com](mailto:alex@example.com) |
| 2  | Sam  | [sam@example.com](mailto:sam@example.com)   |

Now let’s add another user.



## Insert a new row

Type:

```sql
INSERT INTO users (name, email)
VALUES ('Taylor', 'taylor@example.com');
```

Nothing dramatic happens.

That’s normal.

Databases often respond quietly when things succeed.

Now verify it:

```sql
SELECT * FROM users;
```

You should see a new row.



## What just happened?

Let’s break it down carefully.

```sql
INSERT INTO users
```

Means:

> “Add something to the users table.”

```sql
(name, email)
```

Means:

> “These are the columns I’m providing values for.”

```sql
VALUES ('Taylor', 'taylor@example.com')
```

Means:

> “Here are the actual values.”

The order matters:

* first value → first column
* second value → second column



## Why we didn’t include `id`

Notice we never wrote:

```sql
id
```

That’s intentional.

Remember:

* IDs are managed by the database
* databases create them automatically
* you usually should not manually assign them

The database handled identity for us.

That’s a good thing.



## Strings need quotes

Text values are wrapped in quotes:

```sql
'Taylor'
```

Without quotes, SQL assumes:

* it’s a column name
* or some other SQL keyword

This is one of the most common beginner mistakes.



## SQL is strict about structure

Try this intentionally wrong example:

```sql
INSERT INTO users (name)
VALUES ('Jordan', 'jordan@example.com');
```

Notice:

* one column
* two values

The database will complain.

That strictness protects your data.



## Why databases are intentionally strict

Imagine if databases silently accepted broken data.

You’d eventually get:

* missing information
* corrupted rows
* impossible-to-debug issues

Strictness is one reason databases are reliable.

At first it feels annoying.

Later, you’ll appreciate it.



## Adding multiple rows

You can insert more than one row at once.

Example:

```sql
INSERT INTO users (name, email)
VALUES
  ('Jamie', 'jamie@example.com'),
  ('Chris', 'chris@example.com');
```

This creates two rows in one command.

Not required for beginners — but useful to know.



## A very important mindset shift

When using databases, you are usually not thinking:

> “Create object.”

You are thinking:

> “Create a row in a structured system.”

That sounds subtle, but it matters.

Databases care deeply about:

* consistency
* structure
* relationships

Not just temporary objects in memory.



## Let’s verify everything again

Run:

```sql
SELECT * FROM users;
```

Your table should now contain several rows.

Pause and notice something important:

> The database remembers everything even after commands finish.

This persistence is the whole point.



## Common beginner mistakes with `INSERT`

#### Forgetting quotes around text

Wrong:

```sql
VALUES (Taylor)
```

Correct:

```sql
VALUES ('Taylor')
```

#### Mismatching columns and values

Wrong:

```sql
(name)
VALUES ('Taylor', 'email@example.com')
```

The counts must match.

#### Forgetting the semicolon

SQLite waits for commands to finish.

Always end SQL statements with:

```sql
;
```



## What you should practice right now

Before moving on:

* add 3–5 more users
* run `SELECT * FROM users;`
* try selecting only names
* intentionally make a mistake once and read the error

Learning to see errors calmly is part of confidence.



## What you should take away from this article

You now understand:

* how rows are created
* how values map to columns
* why databases enforce structure
* why IDs are usually automatic

This is real database work already.



## What’s next

Now that you can:

* create data
* read data

…the next step is inevitable:

> “How do we change existing data?”

And just as importantly:

> “How do we avoid changing the wrong data?”

That’s where things become more serious.



## Up next

➡ **Article 9 — Changing and Removing Data (Safely)**
