# 📘 Article 10 — When Data Is Connected

## So far, everything has lived in one table

Until now, we’ve worked with a single table:

```
users
```

That was enough to learn:

* rows
* columns
* IDs
* filtering
* updating

But real applications are never that isolated.

A real app has relationships between things.

For example:

* one user can place many orders
* one shop can have many products
* one blog post can have many comments

This is where databases become truly powerful.



## Data rarely lives alone

Think about an online store.

You would not store everything in one giant table like:

| user\_name | user\_email | product\_name | order\_total |
| ---------- | ----------- | ------------- | ------------ |

Why?

Because:

* users repeat
* products repeat
* orders repeat

The data becomes messy very quickly.

Instead, databases organize related data into separate tables.



## Thinking in connected tables

A better structure might look like:

#### users table

| id | name |
| -- | ---- |
| 1  | Alex |

#### orders table

| id | user\_id | total |
| -- | -------- | ----- |
| 1  | 1        | 99.99 |
| 2  | 1        | 49.99 |

Notice something important:

The `orders` table does not store:

* the user’s name
* the user’s email

Instead, it stores:

```
user_id
```

That value points to a user.

This is the foundation of relationships.



## One-to-many relationships

This is the most common relationship type.

Example:

> One user → many orders

Meaning:

* one user can appear in many orders
* each order belongs to one user

This is called:

> a one-to-many relationship

You will see this pattern everywhere.



## The role of IDs becomes much clearer now

Earlier we said:

> IDs exist to give rows stable identity.

Now you can see _why_.

Because relationships depend on stable identity.

Example:

```
orders.user_id = users.id
```

This creates a connection between tables.

Without IDs:

* relationships would break easily
* names changing would cause chaos
* data would become unreliable



## Foreign keys (gentle introduction)

When one table stores another table’s ID, that column is called a:

> foreign key

Example:

```
orders.user_id
```

Why?

Because:

* it references another table
* it points “outside” its own table

Think of it as:

> a link between tables



### Important distinction

#### Primary key

The table’s own identity.

Example:

```
users.id
```

#### Foreign key

A reference to another table’s identity.

Example:

```
orders.user_id
```

This distinction matters a lot later.



## Relationships reduce duplication

Imagine storing user names inside every order.

Problems appear immediately:

* what if the user changes their name?
* what if the email changes?
* what if the same user has 1,000 orders?

You’d have duplicated information everywhere.

Relationships solve this elegantly.

Instead:

* store the user once
* reference the user by ID

This keeps data:

* smaller
* cleaner
* more consistent



## Real-world applications are mostly relationships

Once you notice this, you’ll see it everywhere.

Examples:

| Relationship        | Meaning                             |
| ------------------- | ----------------------------------- |
| shop → products     | one shop has many products          |
| post → comments     | one post has many comments          |
| customer → orders   | one customer places many orders     |
| category → products | one category contains many products |

Relational databases are literally named after:

> relationships between data



## We are intentionally NOT doing JOINs yet

You might already be wondering:

> “Okay, but how do we actually _combine_ related tables?”

That’s the next article.

Right now, we only care about:

* understanding relationships conceptually
* seeing why IDs matter
* seeing why tables connect

If this mental model is strong, JOINs become dramatically easier.



## Let’s create a second table

Open SQLite and try this:

```sql
CREATE TABLE orders (
  id INTEGER PRIMARY KEY,
  user_id INTEGER,
  total REAL
);
```

Now check:

```sql
.tables
```

You should now have:

```
orders
users
```



## Add some orders

Example:

```sql
INSERT INTO orders (user_id, total)
VALUES (1, 99.99);
```

And another:

```sql
INSERT INTO orders (user_id, total)
VALUES (1, 49.99);
```

Now view them:

```sql
SELECT * FROM orders;
```

You should see something like:

| id | user\_id | total |
| -- | -------- | ----- |
| 1  | 1        | 99.99 |
| 2  | 1        | 49.99 |

## Pause and notice what happened

This is huge.

You now have:

* two separate tables
* connected by IDs
* representing real relationships

This is the heart of relational databases.



## What you should take away from this article

Remember these ideas carefully:

* real data is connected
* tables represent different kinds of things
* IDs allow relationships to exist
* foreign keys create connections
* relationships reduce duplication

This is one of the biggest conceptual milestones in SQL.



## What’s next

Now comes the question everyone eventually asks:

> “How do I see related data together?”

For example:

* show orders _with_ user names
* combine products with categories
* connect shops with orders

That’s exactly what JOINs are for.

And despite their reputation, they are much simpler than they first appear.



## Up next

➡ **Article 11 — Seeing Data Together (JOINs, Finally)**
