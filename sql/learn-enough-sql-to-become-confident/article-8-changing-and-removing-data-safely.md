# 📘 Article 8 — Changing and Removing Data (Safely)

## Until now, we’ve only added data

So far, every action has been safe and additive.

We:

* created tables
* inserted rows
* selected data

Nothing was overwritten.\
Nothing disappeared.

But real applications constantly need to:

* update user information
* change order status
* rename products
* remove old records

This is where databases become more serious.

And where beginners often make their first dangerous mistakes.



## Changing data with `UPDATE`

The SQL keyword for changing existing rows is:

```sql
UPDATE
```

Think of it as:

> “Find existing rows and change specific values.”



## Basic structure of `UPDATE`

```sql
UPDATE table_name
SET column = value
WHERE condition;
```

Read it slowly:

> “Update this table, set this value, but only where this condition is true.”

The `WHERE` part is extremely important.

We’ll come back to it soon.



## Let’s change a user’s name

First, see your current users:

```sql
SELECT * FROM users;
```

Suppose you see:

| id | name | email                                       |
| -- | ---- | ------------------------------------------- |
| 1  | Alex | [alex@example.com](mailto:alex@example.com) |

Now run:

```sql
UPDATE users
SET name = 'Alexander'
WHERE id = 1;
```

Then verify:

```sql
SELECT * FROM users;
```

You should now see:

| id | name      | email                                       |
| -- | --------- | ------------------------------------------- |
| 1  | Alexander | [alex@example.com](mailto:alex@example.com) |

You changed one row safely.



## What just happened?

Let’s break it apart.

***

#### `UPDATE users`

Means:

> “We are modifying rows in the users table.”

***

#### `SET name = 'Alexander'`

Means:

> “Change the name column to this value.”

***

#### `WHERE id = 1`

Means:

> “Only apply this change to rows matching this condition.”

This part protects the rest of the table.



## The most dangerous beginner mistake

Look at this carefully:

```sql
UPDATE users
SET name = 'Oops';
```

Notice what’s missing?

No `WHERE`.

That means:

> “Update ALL rows.”

Every user’s name becomes:

```
Oops
```

This is one of the most common SQL mistakes in the world.



## Why `WHERE` matters so much

`WHERE` acts like a filter.

Without it:

* every row matches
* every row changes
* every row can be deleted

This is why experienced developers become emotionally attached to `WHERE`.



## Always preview before changing data

A very good habit:

Before running:

```sql
UPDATE ...
```

Run:

```sql
SELECT ...
```

Using the same condition.

Example:

```sql
SELECT * FROM users
WHERE id = 1;
```

If the result looks correct, _then_ update it.

This habit prevents many production mistakes later.



## Removing data with `DELETE`

Now let’s talk about removing rows.

The keyword is:

```sql
DELETE
```



### Basic structure of `DELETE`

```sql
DELETE FROM table_name
WHERE condition;
```

Read it like this:

> “Delete rows from this table where this condition is true.”

Again:

* the `WHERE` clause matters enormously



### Delete one user safely

Example:

```sql
DELETE FROM users
WHERE id = 2;
```

Then verify:

```sql
SELECT * FROM users;
```

The row with `id = 2` should be gone.



### The second dangerous beginner mistake

This is catastrophic:

```sql
DELETE FROM users;
```

No `WHERE`.

Meaning:

> “Delete every row.”

The table still exists.\
But all data is gone.

This is why people fear databases.



### A calming truth

Even experienced developers:

* make SQL mistakes
* forget `WHERE`
* run unintended queries

The goal is not perfection.

The goal is:

> slowing down enough to think carefully

That’s a professional habit.



### SQL is powerful because it trusts you

Databases assume:

* you know what you’re doing
* you mean what you typed

That power is what makes SQL useful.

But power requires attention.



## Practice safely

Try these:

#### Update a user

```sql
UPDATE users
SET email = 'newemail@example.com'
WHERE id = 1;
```

#### Delete a user

```sql
DELETE FROM users
WHERE id = 3;
```

#### Verify after every change

```sql
SELECT * FROM users;
```



## A beginner-safe workflow (important)

Get used to this rhythm:

1. `SELECT` first
2. Verify target rows
3. `UPDATE` or `DELETE` carefully
4. `SELECT` again

This habit becomes invaluable later.



## What you should take away from this article

You now understand:

* how existing rows are changed
* how rows are removed
* why `WHERE` is critical
* why databases feel strict and serious

You are no longer just reading data.

You are managing it.

That’s a major step.



## What’s next

So far, we’ve mostly worked with:

* all rows
* simple lookups

But real applications constantly ask questions like:

* “Show only active users”
* “Find orders above $100”
* “Get the newest records”
* “Show only products in stock”

To do that, we need filtering and sorting.



## Up next

➡ **Article 9 — Finding the Right Data**
