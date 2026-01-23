# � Article 4 — Giving Every Row an Identity

## Why identity matters

Imagine you have a list of people:

* Alex
* Alex
* Alex

Now imagine someone asks:

> “Update Alex’s email address.”

Which Alex?

This problem exists **everywhere**, not just in databases.

Databases solve it in a very deliberate way.



## Names are not reliable identifiers

In real life:

* names repeat
* emails change
* usernames can be updated

If we tried to identify things by “human” properties, data would quickly break.

So databases use a simple rule:

> **Every row must have a stable identity that never changes.**

That identity is called an **ID**.



## What an ID really is

An ID is:

* just a number (usually)
* unique within a table
* assigned by the database
* never reused
* never changed

It doesn’t describe the thing.

It just **points to it**.

Think of it like:

* a receipt number
* a tracking number
* a ticket number

The number itself means nothing.\
Its **job** is to be unique.



## Why IDs are boring on purpose

IDs are intentionally:

* meaningless
* boring
* machine-friendly

That’s a good thing.

If IDs had meaning:

* changing that meaning would break references
* relationships would become fragile

By keeping IDs boring, databases stay stable.



## One table, one identity system

Important rule:

> **IDs are unique inside a table, not across the whole database.**

That means:

* a user can have `id = 1`
* an order can also have `id = 1`

And that’s perfectly fine.

Identity is always **relative to the table**.



## How databases create IDs

Most databases:

* automatically generate IDs
* assign the next available number
* guarantee uniqueness

You don’t have to manage this manually.

You just trust the database.

This is why IDs feel “invisible” in many apps — because they usually are.



## Why IDs appear everywhere in apps

Once something has an ID, it can be:

* referenced
* linked
* updated
* deleted safely

This is why you see:

* `userId`
* `orderId`
* `shopId`
* `sessionId`

These are not random.

They are how databases connect data **without confusion**.



## IDs vs properties (important distinction)

A row usually has:

* one ID (identity)
* many properties (data)

For example:

* ID: never changes
* email: can change
* name: can change
* status: can change

This separation is critical.

It allows:

* safe updates
* reliable references
* long-lived relationships



## What we are _not_ doing yet

We are still:

* not writing SQL
* not defining tables
* not installing tools

We are strengthening the mental model.

This is intentional.



## What you should take away from this article

If IDs finally make sense now, you’re doing great.

Remember:

* IDs exist to remove ambiguity
* IDs are stable and boring
* IDs are how databases stay sane
* You don’t manage IDs — databases do

Once this clicks, a lot of confusion disappears later.



## What’s next

Now that you understand:

* tables
* rows
* columns
* identity

You’re finally ready to touch something real.

In the next article, we’ll:

* install the simplest SQL environment
* run your first command
* see data with your own eyes

No fear.\
No setup pain.

## Up next

➡ **Article 5 — Getting Set Up: Installing SQL on macOS**
