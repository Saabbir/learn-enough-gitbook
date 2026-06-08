# 📘 Article 14 — Common Mistakes Everyone Makes

## First: mistakes are not a sign you’re bad at SQL

This matters more than you think.

Almost everyone learning SQL eventually experiences:

* confusion
* empty results
* wrong results
* accidental updates
* broken queries
* fear of touching data

That is normal.

SQL feels strict because:

> databases care deeply about correctness

And honestly?\
That strictness is one reason databases are trusted so much.

This article exists to help you:

* make mistakes calmly
* understand _why_ they happen
* build safer habits early



## Mistake 1 — Forgetting `WHERE`

This is the most famous SQL mistake.

Example:

```sql
UPDATE users
SET name = 'Oops';
```

What happened?

Every row changed.

Why?

Because no filtering condition was provided.



### The same danger exists with `DELETE`

```sql
DELETE FROM users;
```

This means:

> “Delete all rows.”

Not:

* one row
* selected rows

All rows.



### The beginner-safe habit

Before:

```sql
UPDATE
```

or

```sql
DELETE
```

Always run a `SELECT` first.

Example:

```sql
SELECT * FROM users
WHERE id = 1;
```

If the result looks correct:

* then update
* then delete

This single habit prevents countless mistakes.



## Mistake 2 — Expecting order without `ORDER BY`

Many beginners assume:

```sql
SELECT * FROM users;
```

…will always return rows in the same order.

That is not guaranteed.

Databases may:

* reorder rows
* optimize internally
* return data differently later

If order matters:

> ask for it explicitly

Example:

```sql
SELECT * FROM users
ORDER BY id DESC;
```



## Mistake 3 — Confusing rows and tables

Beginners sometimes mentally treat:

* a table
* and one row

…as the same thing.

But they are very different.



### A table is:

> a collection of things

Example:

```
users
```



### A row is:

> one specific thing

Example:

```
Alex
```

Keeping this distinction clear makes SQL easier later.



## Mistake 4 — Thinking SQL works step-by-step like JavaScript

This is subtle but extremely important.

JavaScript often feels procedural:

```js
doThis();
thenDoThis();
thenLoop();
```

SQL is different.

You describe:

> the result you want

Example:

```sql
SELECT * FROM users
WHERE id > 2
ORDER BY name;
```

You are not manually:

* looping
* filtering
* sorting

The database decides _how_ to execute the query internally.

This is called:

> declarative thinking

And it takes time to feel natural.



## Mistake 5 — Being afraid of errors

This is psychological, but important.

Many beginners see:

```
syntax error
```

…and panic.

But database errors are often:

* specific
* helpful
* protective

Errors usually mean:

> “The database prevented something invalid.”

That’s a good thing.



## Mistake 6 — Forgetting quotes around text

Wrong:

```sql
WHERE name = Alex
```

Correct:

```sql
WHERE name = 'Alex'
```

Without quotes, SQL assumes:

* `Alex` is a column name
* or keyword

This is one of the most common beginner syntax mistakes.



## Mistake 7 — Mixing unrelated data together

Example of bad table design:

| name | product | color | order\_total |
| ---- | ------- | ----- | ------------ |

This mixes:

* users
* products
* orders

…into one structure.

Relational databases work best when:

> one table represents one kind of thing

Clean structure makes:

* relationships easier
* queries simpler
* updates safer



## Mistake 8 — Avoiding JOINs because they look scary

Many beginners try to:

* duplicate data
* avoid relationships
* store everything in one table

…because JOINs seem intimidating.

But now you know:

> JOINs are just matching related rows

Avoiding relationships usually creates more problems later.



## Mistake 9 — Memorizing instead of understanding

This is very common.

People try to memorize:

* syntax
* command order
* keywords

But SQL becomes easier when you focus on:

* mental models
* meaning
* structure
* reading queries naturally

You do not need perfect memory.

You need:

> calm understanding



## Mistake 10 — Thinking you must learn “all of SQL”

This is one of the biggest misconceptions.

Most application developers repeatedly use:

* `SELECT`
* `INSERT`
* `UPDATE`
* `DELETE`
* `WHERE`
* `JOIN`
* `GROUP BY`

That’s the practical core.

You do not need:

* every keyword
* advanced analytics
* database wizardry

Confidence comes from:

> understanding the fundamentals deeply

Not from memorizing obscure features.



## A very important truth

Even experienced developers:

* check documentation
* make query mistakes
* forget syntax
* test queries carefully

The goal is not:

> “Never make mistakes.”

The goal is:

> “Understand mistakes calmly and recover safely.”

That is real confidence.



## What you should take away from this article

Remember these ideas:

* mistakes are normal
* `WHERE` matters enormously
* SQL is declarative
* ordering must be explicit
* relationships are good
* understanding beats memorization

If SQL feels less intimidating now, this article did its job.



## What’s next

You’ve now learned many SQL concepts:

* tables
* rows
* filtering
* JOINs
* aggregation
* relationships

Before we wrap up the learning journey, it’s helpful to create a small reference guide for all the terminology you’ve encountered.

Something you can revisit anytime.



## Up next

➡ **Article 15 — A Small SQL Vocabulary (Glossary)**
