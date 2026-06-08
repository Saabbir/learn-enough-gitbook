# 📘 Article 4 — Tables: Where Data Lives

## Before SQL, we need one very important idea

So far, we’ve talked about:

* databases
* organized storage
* rows and columns briefly

But now we need to slow down and focus on the most important structure in relational databases:

> the table

Almost everything in SQL revolves around tables.

If tables feel intuitive, SQL becomes dramatically easier later.



## A table represents one kind of thing

This is the first important mental model.

A table should represent:

> one category of thing

Examples:

| Table Name | Represents          |
| ---------- | ------------------- |
| users      | people using an app |
| products   | items for sale      |
| orders     | purchases           |
| comments   | user comments       |

A table is not:

* random data
* mixed concepts
* unrelated information

It’s one organized collection of similar things.



## Think of a table as a spreadsheet (but stricter)

This comparison helps beginners a lot.

A database table looks similar to:

* Excel
* Google Sheets
* Airtable

You have:

* columns
* rows
* cells

But databases are much stricter about:

* consistency
* structure
* relationships
* data integrity

That strictness is why databases are reliable.



## Rows represent individual things

Inside a table, each row represents:

> one real thing

Example:

#### users table

| id | name | email                                       |
| -- | ---- | ------------------------------------------- |
| 1  | Alex | [alex@example.com](mailto:alex@example.com) |
| 2  | Sam  | [sam@example.com](mailto:sam@example.com)   |

Each row is:

* one user
* one identity
* one record

Not multiple users.\
Not partial users.

Just one.



## Columns describe properties

Columns describe:

> what information each row contains

In the `users` table:

| Column | Meaning       |
| ------ | ------------- |
| id     | user identity |
| name   | user name     |
| email  | user email    |

Columns define the structure of the table.

Every row follows the same structure.

That consistency matters enormously.



## Why tables need consistency

Imagine a chaotic table like this:

| name | product | shoe\_size | favorite\_color |
| ---- | ------- | ---------- | --------------- |

Some rows are users.\
Some are products.\
Some are random information.

Very quickly:

* searching becomes difficult
* relationships become confusing
* updates become dangerous

Databases avoid this by enforcing structure.



## Tables are designed intentionally

Good table design asks:

> “What kind of thing am I storing?”

Then:

* choose meaningful columns
* keep the structure focused
* avoid mixing unrelated concepts

This becomes second nature later.



## Naming tables

Most table names are:

* lowercase
* plural

Examples:

```
users
orders
products
comments
```

Why plural?

Because tables store:

> many rows

Not just one.

This is convention, not law — but it’s widely used.



## Thinking in records instead of objects

If you come from JavaScript, this mental shift matters.

You might think:

```js
const user = {
  name: "Alex",
  email: "alex@example.com"
};
```

Databases think differently.

They think:

* collections
* records
* structured storage
* querying groups of things

Not temporary objects in memory.



## A table is both simple and powerful

At a glance, a table looks boring.

But tables enable:

* filtering
* sorting
* searching
* relationships
* aggregation
* consistency

Almost everything in relational databases starts here.



## Visualizing tables mentally

Try practicing this mentally.

Imagine:

* a `products` table
* a `posts` table
* an `orders` table

Ask yourself:

* What does one row represent?
* What columns would exist?
* What information belongs there?

This exercise builds database intuition quickly.



## Example: designing a products table conceptually

Suppose you’re building an online store.

A `products` table might contain:

| Column | Meaning          |
| ------ | ---------------- |
| id     | product identity |
| name   | product name     |
| price  | product price    |
| stock  | inventory amount |

Notice:

* every row follows the same structure
* every product has the same kinds of data

That predictability is the entire point.



## What tables are NOT

A table is not:

* a page
* a UI component
* an API response
* a JavaScript object

Tables represent:

> structured stored data

This distinction becomes important later when building applications.



## Why beginners sometimes struggle here

Because many tutorials:

* jump directly into SQL syntax
* skip the mental model
* assume tables are obvious

But understanding tables deeply makes everything easier:

* IDs
* relationships
* JOINs
* Prisma schemas later
* Shopify app databases later

This foundation matters.



## What you should take away from this article

Pause and lock these ideas in:

* a table represents one kind of thing
* rows represent individual records
* columns represent properties
* consistency is essential
* good tables are intentional

If this feels intuitive now, you’re in a very good place.



## What’s next

Now that tables feel concrete, we can answer a critical question:

> “How does the database tell rows apart safely?”

That’s where IDs finally become important.

And once IDs click, relationships become much easier later.



## Up next

➡ **Article 5 — Giving Every Row an Identity**
