# 📘 CHAPTER 1 — The Problem Webhooks Solve

Before learning what a **webhook** is, we first need to understand **the problem it solves**.

Most beginners struggle with webhooks because they jump straight into the definition without understanding **why they exist**.

This chapter focuses only on that.



## Imagine You Are Building an App

Suppose you are building an application that connects with an external platform like:

* **Shopify**
* **GitHub**
* **Stripe**

Your app runs on **your own server**, separate from these platforms.

But your application needs to know when something happens.

Examples:

| Platform | Event you care about       |
| -------- | -------------------------- |
| Shopify  | A customer places an order |
| GitHub   | Someone pushes new code    |
| Stripe   | A payment succeeds         |

So the question becomes:

> How will your application know when these events happen?



## The First Solution Most Beginners Think Of

A very common beginner idea is this:

Your application repeatedly asks the platform if anything new happened.

Example:

```
Did any new orders happen?
Did any new orders happen?
Did any new orders happen?
```

Your server keeps checking the platform again and again.

This approach is called **polling**.



## What Is Polling?

Polling means **repeatedly asking a server if something changed**.

Your application might check every few seconds.

Example flow:

```
Your App → Platform API → Any new events?
```

Then after a few seconds:

```
Your App → Platform API → Any new events?
```

Then again:

```
Your App → Platform API → Any new events?
```



## Simple Code Example of Polling

Imagine checking every 10 seconds.

```javascript
setInterval(async () => {
  const response = await fetch("https://api.example.com/orders")
  const orders = await response.json()

  console.log("Checking for new orders...")
}, 10000)
```

This runs every **10 seconds**.

Your app keeps asking the server whether anything changed.



## Why Polling Is Inefficient

At first glance polling seems simple, but it causes serious problems.

| Problem          | Explanation                           |
| ---------------- | ------------------------------------- |
| Wasted requests  | Most requests return nothing          |
| High server load | Platforms receive unnecessary traffic |
| Delayed updates  | Events are detected late              |
| Poor scalability | Doesn't work well for large systems   |

Imagine **10,000 applications** constantly asking a platform:

```
Any updates?
Any updates?
Any updates?
Any updates?
```

Even when **nothing happened**.

This wastes resources for both sides.



## Real-World Analogy

Imagine you ordered food from a restaurant.

Polling would look like this:

You call the restaurant every 2 minutes.

```
Is my food ready?
Is my food ready?
Is my food ready?
```

The restaurant staff would get annoyed very quickly.

And most of the time the answer would be:

```
No, not yet.
```



## A Much Better Solution

Instead of calling repeatedly, you could say:

> “Please call me when my food is ready.”

Now the restaurant contacts you **only when the event happens**.

No unnecessary calls.

No wasted effort.

This is the key idea behind **webhooks**.



## The Idea That Led to Webhooks

Instead of the client constantly asking the server:

```
Client → Server → Any updates?
```

The server simply sends a message when something happens.

```
Event happens
      ↓
Server sends notification
      ↓
Your application receives it
```

This automatic notification is what we call a **webhook**.



## Real Example (Shopify)

Let’s revisit our earlier example using **Shopify**.

A customer buys a product.

```
Customer places order
        ↓
Shopify creates order
```

Your app needs to know this.

Instead of your app constantly checking Shopify, Shopify could simply notify you when it happens.

```
Customer buys product
        ↓
Shopify sends notification
        ↓
Your app receives order data
```

That notification is a **webhook**.



## Another Example (GitHub)

Consider **GitHub**.

A developer pushes new code.

```
Developer pushes code
        ↓
GitHub detects event
        ↓
GitHub notifies another service
```

That notification might trigger:

* automated tests
* deployment
* notifications

Again, this notification is a **webhook**.



## Key Idea of This Chapter

Webhooks exist to solve a simple but important problem:

```
Polling is inefficient.
```

Instead of constantly asking for updates:

```
Client → Server → Any updates?
```

The server sends updates **only when something happens**.



## Mental Model to Remember

When you hear **webhook**, think about this idea:

```
Something happened
        ↓
Server automatically notifies another system
```



## Quick Recap

Polling approach:

```
Client repeatedly asks server for updates
```

Webhook approach:

```
Server sends notification when event occurs
```

Webhooks eliminate **unnecessary requests** and enable **real-time automation**.



## Next Chapter

Now that you understand the **problem webhooks solve**, the next question is:

> **What exactly is a webhook?**

In the next chapter we will learn:

* the precise definition of a webhook
* what a webhook request looks like
* how webhook data is delivered

This is where the concept becomes much clearer.
