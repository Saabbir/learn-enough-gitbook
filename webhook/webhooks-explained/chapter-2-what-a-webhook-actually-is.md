# 📘 CHAPTER 2 — What a Webhook Actually Is

In the previous chapter, we learned **why webhooks exist**.

The main problem was **polling** — repeatedly asking a server if something happened.

Webhooks solve this problem by allowing systems to **send updates automatically**.

Now we will answer the most important question:

> **What exactly is a webhook?**



## The Simple Definition

A webhook is:

> **An HTTP request automatically sent when an event occurs.**

That’s it.

When something happens in a system, that system sends an **HTTP request** to another system.



## The Core Formula

You can understand almost every webhook with this simple formula:

```
EVENT → HTTP REQUEST → YOUR SERVER
```

Example:

```
Order created
      ↓
Webhook sent
      ↓
Your application receives the order data
```



## A Simple Example

Imagine a system detects an event.

For example:

```
order.created
```

The system then sends an HTTP request to your server.

Example request:

```
POST https://yourapp.com/webhook
```

With data:

```json
{
  "event": "order.created",
  "order_id": 12345
}
```

Your server receives this data and can react to it.



## What Is an HTTP Request?

To understand webhooks, you need to know one basic concept: **HTTP requests**.

When a browser loads a website, it sends an HTTP request.

Example:

```
GET https://example.com
```

The server then returns the webpage.

Webhooks use the same mechanism, but instead of a browser sending the request, **a platform sends it automatically**.



## Webhooks Use Normal Web Technology

This is important:

A webhook is **not a special protocol**.

It is simply a normal HTTP request.

Usually it looks like this:

```
POST /webhook
```

The request contains:

| Part    | Purpose                    |
| ------- | -------------------------- |
| URL     | where the request is sent  |
| Method  | usually POST               |
| Headers | metadata about the request |
| Body    | the event data             |

We will explore these parts later in the series.



## Real Example — Shopify Webhook

Let’s look at a real platform example using **Shopify**.

Suppose a customer places an order.

```
Customer buys product
        ↓
Shopify creates order
```

Shopify then sends a webhook request.

Example:

```
POST https://yourapp.com/webhooks/orders-create
```

Payload example:

```json
{
  "id": 938274,
  "email": "customer@email.com",
  "total_price": "120.00"
}
```

Your server receives this order information immediately.

Your application could then:

* store the order
* notify a warehouse
* update analytics
* trigger an email

All automatically.



## Another Real Example — GitHub Webhook

Now consider **GitHub**.

A developer pushes new code to a repository.

```
Developer pushes code
        ↓
GitHub detects event
```

GitHub sends a webhook.

Example request:

```
POST https://ci-server.com/github-webhook
```

Payload example:

```json
{
  "ref": "refs/heads/main",
  "repository": {
    "name": "my-project"
  },
  "pusher": {
    "name": "saabbir"
  }
}
```

This webhook could trigger:

* automated tests
* code deployment
* notifications to a team chat

This is how many **CI/CD pipelines** work.



## Key Characteristics of Webhooks

Most webhooks share the same characteristics.

| Characteristic | Meaning                          |
| -------------- | -------------------------------- |
| Event-driven   | Triggered when something happens |
| Automatic      | No manual request needed         |
| HTTP-based     | Uses standard web requests       |
| Real-time      | Delivered immediately            |



## Visual Flow of a Webhook

A webhook interaction usually looks like this:

```
Event occurs
      ↓
Platform detects event
      ↓
Platform sends HTTP request
      ↓
Your server receives the request
      ↓
Your code processes the data
```



## Why Webhooks Are Powerful

Without webhooks:

```
App constantly checks for updates
```

With webhooks:

```
App waits
Event happens
Platform notifies the app
```

This makes systems:

* faster
* more efficient
* easier to automate



## Important Mental Model

Whenever you hear **webhook**, remember this:

```
An event triggered an HTTP request.
```

For example:

```
payment.succeeded
      ↓
Webhook sent
```

Or:

```
order.created
      ↓
Webhook sent
```



## Quick Recap

A webhook is:

```
An automatic HTTP request sent when an event occurs.
```

It usually includes:

* a URL
* a POST request
* event data

Webhooks allow systems to **communicate automatically when events happen**.



## Next Chapter

Now that we know **what a webhook is**, we need to clarify a common confusion:

> **How are webhooks different from APIs?**

In the next chapter we will explain:

* API requests vs webhook requests
* push vs pull communication
* when to use APIs vs webhooks

This comparison is where many developers finally say:

**“Now webhooks make sense.”**
