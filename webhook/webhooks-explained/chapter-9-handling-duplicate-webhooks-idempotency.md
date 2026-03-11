# 📘 CHAPTER 9 — Handling Duplicate Webhooks (Idempotency)

By now you can:

* receive webhooks
* inspect payloads
* verify webhook security

But in real systems another problem appears quickly.

> **Webhooks can be delivered multiple times.**

This surprises many beginners.

But it is **normal behavior** for most webhook providers.

Platforms like:

* Shopify
* GitHub
* Stripe

**retry webhooks if your server does not respond correctly**.

This means the same event might arrive **more than once**.



## Why Platforms Retry Webhooks

Platforms retry webhooks to ensure **reliable delivery**.

Example situation:

```
Platform sends webhook
        ↓
Your server crashes
        ↓
Platform receives no response
        ↓
Platform retries webhook
```

From the platform’s perspective:

> “Maybe the server temporarily failed. Let’s try again.”

This behavior makes webhooks **more reliable**, but it introduces a new challenge.



## The Duplicate Webhook Problem

Suppose your system processes a webhook like this.

```
Order Created Webhook
        ↓
Save order to database
```

Now imagine the webhook is delivered twice.

```
Webhook 1 → Save order
Webhook 2 → Save order again
```

This could cause:

| Problem           | Example                  |
| ----------------- | ------------------------ |
| Duplicate records | Same order saved twice   |
| Repeated actions  | Email sent twice         |
| Double processing | Workflow triggered twice |

In payment systems this could even lead to **double charges**.



## Real Example — Shopify

Suppose **Shopify** sends an order webhook.

Payload example:

```json
{
  "id": 938274,
  "email": "customer@email.com",
  "total_price": "120.00"
}
```

If your server crashes during processing:

```
Shopify sends webhook
        ↓
Server fails
        ↓
Shopify retries webhook
```

Now the same order webhook arrives again.

Your system must **recognize that it already processed this event**.



## The Solution — Idempotency

To solve this problem, webhook handlers must be **idempotent**.

Idempotency means:

> **Processing the same event multiple times produces the same result.**

Example:

```
Process order #123
Process order #123 again
```

The result should still be:

```
Order #123 processed only once
```



## Using Event IDs

Most webhook payloads include a unique identifier.

Example Shopify order ID:

```json
{
  "id": 938274
}
```

Your system can store this ID in a database.

Before processing a webhook, check if the ID already exists.

Flow:

```
Webhook received
        ↓
Check database for event ID
        ↓
Already processed?
        ↓
Yes → ignore
No → process event
```



## Example Implementation (Node.js)

Simple example using a database check.

```javascript
app.post("/webhook", async (req, res) => {

  const order = req.body
  const orderId = order.id

  const exists = await db.orders.findOne({ id: orderId })

  if (exists) {
    console.log("Duplicate webhook ignored")
    return res.status(200).send("Already processed")
  }

  await db.orders.insert({ id: orderId })

  console.log("Order processed")

  res.status(200).send("OK")

})
```

Now duplicate webhooks will not cause problems.



## Example — GitHub Push Events

With **GitHub**, push events also contain identifiers.

Example payload:

```json
{
  "after": "b6589fc6ab0dc82cf12099d1c2d40ab994e8410c"
}
```

The `after` field represents a commit hash.

Your system can store the commit hash and ignore duplicates.



## Stripe Payment Example

For **Stripe**, webhook events contain an event ID.

Example:

```json
{
  "id": "evt_123456",
  "type": "payment_intent.succeeded"
}
```

Stripe recommends storing the event ID.

If the same event arrives again, skip processing.



## Recommended Database Table

Many webhook systems maintain a table like this.

| Event ID   | Processed At |
| ---------- | ------------ |
| evt\_12345 | 2026-03-11   |
| evt\_12346 | 2026-03-11   |

When a webhook arrives:

1. Check if event ID exists
2. If yes → skip
3. If no → process and store



## Visual Flow

A safe webhook system works like this:

```
Webhook received
      ↓
Verify signature
      ↓
Check event ID
      ↓
Already processed?
      ↓
Yes → ignore
No → process event
```

This ensures your system behaves safely.



## Important Developer Rule

Always assume that webhooks **may arrive multiple times**.

Your webhook handler must be:

```
Safe
Repeatable
Idempotent
```

If your system depends on **single delivery**, it will break in production.



## Quick Recap

Webhook providers retry events to ensure delivery.

This means **duplicate events can happen**.

To handle this safely:

1. Use unique event IDs
2. Store processed events
3. Ignore duplicates

This concept is called **idempotency**.

Platforms that recommend this approach include:

* Shopify
* GitHub
* Stripe



## Next Chapter

We now understand how webhooks work and how to handle them safely.

But large systems process **thousands or millions of webhook events**.

Handling them directly in your server can cause performance problems.

In the next chapter we will learn about:

> **Webhook architecture in production systems**

Including:

* queues
* workers
* scalable event processing

This is how large companies build **reliable webhook pipelines**.
