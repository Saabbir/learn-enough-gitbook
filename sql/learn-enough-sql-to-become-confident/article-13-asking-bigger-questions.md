# 📘 Article 13 — Asking Bigger Questions

## Until now, we’ve mostly looked at rows

Most of our queries have worked like this:

> “Show me these rows.”

Examples:

* users
* orders
* filtered results
* joined data

But real applications often ask a different kind of question.

Not:

> “Show me the rows.”

Instead:

> “Summarize the data.”

For example:

* How many users exist?
* How many orders were placed?
* What is the total revenue?
* Which user placed the most orders?

This is where aggregation comes in.



## What aggregation means

Aggregation means:

> “Take many rows and calculate a summary.”

Instead of returning:

* individual rows

You return:

* counts
* totals
* averages
* grouped summaries

Think:

> “big-picture questions”



## The simplest aggregation: `COUNT`

The most common aggregation function is:

```sql
COUNT()
```

It counts rows.



### Count all users

Try this:

```sql
SELECT COUNT(*)
FROM users;
```

Read it naturally:

> “Count all rows in the users table.”

You might see something like:

| COUNT(\*) |
| --------- |
| 5         |

Meaning:

> there are 5 users



## Why `COUNT(*)` uses `*`

Earlier, `*` meant:

> all columns

Here it means:

> count all rows

Don’t overthink it.\
You’ll get used to the pattern naturally.



## Counting filtered rows

You can combine `COUNT` with `WHERE`.

Example:

```sql
SELECT COUNT(*)
FROM orders
WHERE total > 50;
```

Meaning:

> “Count orders where the total is greater than 50.”

This is already real reporting logic.



## Summing values with `SUM`

Another common aggregation function:

```sql
SUM()
```

This adds numeric values together.



### Calculate total revenue

Try:

```sql
SELECT SUM(total)
FROM orders;
```

Meaning:

> “Add all order totals together.”

You’ll get a single number representing total revenue.



## Average values with `AVG`

Example:

```sql
SELECT AVG(total)
FROM orders;
```

Meaning:

> “Calculate the average order total.”

Again:

* many rows go in
* one summary value comes out



## The important mental shift

Normal queries return:

> rows

Aggregation queries return:

> summaries

That’s the key difference.



## Grouping data with `GROUP BY`

Now we reach one of SQL’s most powerful ideas.

Sometimes you don’t want:

* one total for everything

You want:

* totals per user
* counts per category
* revenue per shop

This is what `GROUP BY` does.



## Example: orders per user

Try this:

```sql
SELECT user_id, COUNT(*)
FROM orders
GROUP BY user_id;
```

Read it slowly:

> “Group orders by user\_id, then count rows in each group.”

You might see:

| user\_id | COUNT(\*) |
| -------- | --------- |
| 1        | 2         |

Meaning:

> user 1 has 2 orders



## Visualizing grouping

Without grouping:

| order\_id | user\_id |
| --------- | -------- |
| 1         | 1        |
| 2         | 1        |
| 3         | 2        |

With `GROUP BY user_id`, SQL conceptually creates:

#### Group 1

* order 1
* order 2

#### Group 2

* order 3

Then aggregation happens _inside each group_.



## Another example: revenue per user

```sql
SELECT user_id, SUM(total)
FROM orders
GROUP BY user_id;
```

Meaning:

> “For each user, calculate total order value.”

This is real business reporting.



## Why aggregation matters so much

Applications constantly ask summary questions:

* dashboards
* analytics
* reports
* admin panels
* statistics

Aggregation is what makes those possible.



## SQL is becoming expressive now

Pause and notice what you can already do:

* store data
* filter data
* sort data
* connect tables
* summarize data

That’s already a very meaningful SQL foundation.



## A very important simplification

There are many advanced SQL topics around aggregation.

We are intentionally skipping:

* window functions
* advanced analytics
* ranking systems
* statistical functions

Because the goal is:

> confidence, not exhaustiveness

And honestly?\
Most application developers use simple aggregation most of the time.



## Practice slowly

Try these:

### Count all orders

```sql
SELECT COUNT(*)
FROM orders;
```

### Total revenue

```sql
SELECT SUM(total)
FROM orders;
```

### Average order value

```sql
SELECT AVG(total)
FROM orders;
```

### Orders per user

```sql
SELECT user_id, COUNT(*)
FROM orders
GROUP BY user_id;
```



## What you should take away from this article

Remember these ideas:

* aggregation summarizes data
* `COUNT` counts rows
* `SUM` adds values
* `AVG` calculates averages
* `GROUP BY` creates categories before aggregation

This is how databases answer “bigger questions.”



## What’s next

You now understand:

* tables
* rows
* filtering
* relationships
* JOINs
* aggregation

That’s already enough to understand many real-world queries.

Before moving forward, we should pause and talk about something equally important:

> mistakes

Because SQL mistakes are normal.\
And understanding them calmly builds real confidence.



## Up next

➡ **Article 14 — Common Mistakes Everyone Makes**
