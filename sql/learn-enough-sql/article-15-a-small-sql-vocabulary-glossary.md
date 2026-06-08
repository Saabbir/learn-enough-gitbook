# 📘 Article 15 — A Small SQL Vocabulary (Glossary)

## Why this glossary exists

When learning SQL, one of the hardest parts is often not the syntax.

It’s the vocabulary.

Words like:

* table
* row
* query
* relationship
* aggregation

…can feel abstract at first.

This glossary is intentionally:

* small
* plain-English
* beginner-friendly

You are not expected to memorize it.

Use it like:

> a calm reference you can revisit anytime



## Database

> A system that stores and organizes data safely over time.

A database allows applications to:

* remember information
* search information
* update information
* keep data consistent

Example:

* user accounts
* products
* orders
* messages



## SQL

> A language used to communicate with relational databases.

SQL is used to:

* read data
* insert data
* update data
* delete data
* summarize data

SQL is not a database itself.



## SQLite

> A lightweight database stored in a single file.

We used SQLite in this series because it:

* requires almost no setup
* is beginner-friendly
* is perfect for practicing SQL



## Table

> An organized collection of similar things.

Examples:

* `users`
* `orders`
* `products`

A table contains:

* rows
* columns



## Row

> One individual record inside a table.

Example:

* one user
* one order
* one product

A row represents:

> one thing



## Column

> A specific property stored for every row.

Example in a `users` table:

| Column | Meaning       |
| ------ | ------------- |
| id     | user identity |
| name   | user name     |
| email  | user email    |



## Record

> Another word for a row.

You will hear both terms used interchangeably.



## Query

> A request sent to the database.

Example:

```sql
SELECT * FROM users;
```

This query asks:

> “Show all users.”



## SELECT

> The SQL keyword used to read data.

Example:

```sql
SELECT name FROM users;
```

Meaning:

> “Show user names.”



## INSERT

> The SQL keyword used to create new rows.

Example:

```sql
INSERT INTO users (name)
VALUES ('Alex');
```



## UPDATE

> The SQL keyword used to modify existing rows.

Example:

```sql
UPDATE users
SET name = 'Sam'
WHERE id = 1;
```



## DELETE

> The SQL keyword used to remove rows.

Example:

```sql
DELETE FROM users
WHERE id = 1;
```



## WHERE

> A filtering condition.

Used to target specific rows.

Example:

```sql
WHERE id = 1
```

Without `WHERE`, operations may affect:

> every row



## ORDER BY

> Sorts query results.

Example:

```sql
ORDER BY name
```



## LIMIT

> Restricts how many rows are returned.

Example:

```sql
LIMIT 5
```



## Primary Key

> A column that uniquely identifies each row.

Usually:

```
id
```

Primary keys:

* must be unique
* should remain stable



## Foreign Key

> A column that references another table’s primary key.

Example:

```
orders.user_id
```

This creates relationships between tables.



## Relationship

> A connection between tables.

Example:

* one user → many orders

Relationships are a core idea in relational databases.



## JOIN

> Combines related rows from multiple tables.

Example:

```sql
SELECT users.name, orders.total
FROM orders
JOIN users
ON orders.user_id = users.id;
```

JOINs allow databases to:

* connect related data
* avoid duplication



## Aggregation

> Summarizing many rows into fewer results.

Examples:

* counting rows
* summing totals
* calculating averages



## COUNT

> Counts rows.

Example:

```sql
SELECT COUNT(*) FROM users;
```



## SUM

> Adds numeric values together.

Example:

```sql
SELECT SUM(total) FROM orders;
```



## GROUP BY

> Splits rows into groups before aggregation.

Example:

```sql
GROUP BY user_id
```



## Result Set

> The data returned by a query.

Example:

* rows shown after a `SELECT`



## Schema

> The structure of a database.

Includes:

* tables
* columns
* relationships

Think of schema as:

> the database blueprint



## Declarative

> Describing _what_ you want instead of _how_ to do it.

SQL is declarative.

You describe:

* the desired result

The database decides:

* how to execute the query



## Relational Database

> A database built around tables and relationships.

Examples:

* PostgreSQL
* MySQL
* SQLite



## SQL vs NoSQL

#### SQL databases

* structured
* relational
* table-based

#### NoSQL databases

* more flexible
* often specialized
* use different storage models

Both are useful in different situations.



## ORM

> A tool that helps applications interact with databases using programming language syntax.

Examples:

* Prisma
* Sequelize

ORMs usually generate SQL behind the scenes.



## Persistence

> Data surviving after the application stops running.

Without persistence:

* refreshing the app would erase everything

Databases provide persistence.



## Final reassurance

You do not need to memorize every term here.

The goal is:

* familiarity
* recognition
* comfort

Over time, these words stop feeling technical and start feeling normal.

That’s what confidence looks like.



## What’s next

You’ve now completed the core SQL learning journey.

Before we move toward PostgreSQL, SQLite deeper, or ORMs, there’s one final conceptual article left:

> learning how databases “think”

This is where:

* SQL vs JavaScript mindset
* sets vs loops
* declarative thinking
* ORMs

…all finally connect together.



## Up next

➡ **Article 16 — Appendix: Thinking Like a Database**
