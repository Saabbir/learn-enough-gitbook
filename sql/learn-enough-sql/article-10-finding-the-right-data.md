# 📘 Article 10 — Finding the Right Data

## Real databases are rarely small

Up to now, your `users` table probably has only a few rows.

That’s manageable.

But real applications may have:

* thousands of users
* millions of orders
* years of data

At that scale, you rarely want:

> “Show me everything.”

You want:

* specific rows
* sorted results
* limited results
* meaningful answers

This is where SQL starts becoming truly useful.



## Filtering data with `WHERE`

The most important filtering keyword is:

```sql
WHERE
```

You already saw it with:

* `UPDATE`
* `DELETE`

But it’s even more common with `SELECT`.



### Basic filtering

Example:

```sql
SELECT * FROM users
WHERE id = 1;
```

Read it naturally:

> “Show me all users where the ID equals 1.”

This returns only matching rows.



### Comparison operators

SQL uses comparison operators to filter data.

The most common ones are:

| Meaning          | Operator |
| ---------------- | -------- |
| equals           | `=`      |
| not equal        | `!=`     |
| greater than     | `>`      |
| less than        | `<`      |
| greater or equal | `>=`     |
| less or equal    | `<=`     |

These work similarly to programming languages — but with SQL syntax.



### Example: exact match

```sql
SELECT * FROM users
WHERE name = 'Alex';
```

This asks:

> “Show users whose name is Alex.”



### Example: not equal

```sql
SELECT * FROM users
WHERE id != 1;
```

Meaning:

> “Show every user except the one with ID 1.”



### Combining conditions with `AND`

Sometimes one condition is not enough.

Example:

```sql
SELECT * FROM users
WHERE name = 'Alex'
AND id = 1;
```

Meaning:

> “Both conditions must be true.”



### Combining conditions with `OR`

```sql
SELECT * FROM users
WHERE name = 'Alex'
OR name = 'Sam';
```

Meaning:

> “Either condition can be true.”



### Important mindset shift

SQL filtering is not:

* looping manually
* checking row by row yourself

You describe:

> the kind of rows you want

The database handles the searching.

This becomes powerful at scale.



## Sorting results with `ORDER BY`

Remember earlier when we said:

> SQL does not guarantee order unless you ask for it.

This is how you ask.



### Basic sorting

```sql
SELECT * FROM users
ORDER BY name;
```

Meaning:

> “Show users sorted by name.”

By default:

* text → alphabetical
* numbers → smallest to largest



### Descending order

To reverse the order:

```sql
SELECT * FROM users
ORDER BY id DESC;
```

`DESC` means:

> descending

Now the highest ID appears first.

This is extremely common in applications:

* newest orders
* latest users
* most recent activity



### Ascending order

You can also write:

```sql
ORDER BY id ASC;
```

`ASC` means:

> ascending

But this is already the default.

So most people omit it.



## Limiting results with `LIMIT`

Sometimes you don’t want:

* every row
* huge result sets

You only want:

* the first few matches

### Basic limit

```sql
SELECT * FROM users
LIMIT 3;
```

Meaning:

> “Show only the first 3 rows.”

Very common for:

* dashboards
* previews
* pagination
* admin panels



## Combining everything together

This is where SQL starts feeling expressive.

Example:

```sql
SELECT * FROM users
WHERE id > 1
ORDER BY id DESC
LIMIT 2;
```

Read it slowly:

> Show users\
> where the ID is greater than 1\
> sorted from highest ID to lowest\
> but only return 2 rows

That’s already a very real query.



### SQL clauses are composable

This is one of SQL’s superpowers.

You combine small concepts:

* `SELECT`
* `WHERE`
* `ORDER BY`
* `LIMIT`

…to describe exactly what you want.

Complex queries are usually just:

> many simple ideas combined together



## Practice ideas

Try these yourself:

#### Show only one user

```sql
SELECT * FROM users
WHERE id = 1;
```

#### Show newest users first

```sql
SELECT * FROM users
ORDER BY id DESC;
```

#### Show only two users

```sql
SELECT * FROM users
LIMIT 2;
```

#### Combine filtering and sorting

```sql
SELECT * FROM users
WHERE id > 1
ORDER BY name;
```



## A subtle but important idea

Notice how SQL reads almost like English:

```sql
SELECT * FROM users
WHERE id > 1
ORDER BY id DESC;
```

This readability is intentional.

SQL was designed to be:

* descriptive
* human-readable
* declarative

Not procedural.



## What you should take away from this article

You now know how to:

* filter rows
* combine conditions
* sort results
* limit output
* compose queries naturally

This is already enough to understand many real-world app queries.



## What’s next

Right now, your data lives in:

* one table
* isolated rows

But real applications connect data together.

For example:

* one shop has many products
* one user has many orders
* one order belongs to one customer

To understand this properly, we need relationships.



## Up next

➡ **Article 11 — When Data Is Connected**
