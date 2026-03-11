# 📘 CHAPTER 3 — Webhooks vs APIs

Now that you understand **what a webhook is**, we need to clear up one of the most common beginner confusions:

> **What is the difference between an API and a webhook?**

Many beginners think they are the same thing.\
They are not.

They are actually **opposite communication patterns**.



## The Short Answer

| Technology | Who Starts the Request         |
| ---------- | ------------------------------ |
| API        | The **client** sends a request |
| Webhook    | The **server** sends a request |

Think of it like this:

```
API     → You ask the server
Webhook → The server notifies you
```



## What Is an API?

An **API (Application Programming Interface)** allows your application to **request data from another system**.

Your application decides **when** to make the request.

Example:

Your app asks a platform for orders.

```http
GET https://api.shopify.com/orders
```

The platform then returns the data.

This is how APIs normally work.



## Example Using Shopify API

Suppose you want to fetch orders from **Shopify**.

Your application sends a request:

```http
GET /admin/api/orders
```

Response example:

```json
{
  "orders": [
    {
      "id": 12345,
      "total_price": "120.00"
    }
  ]
}
```

Your application asked for the data.

So the **request started from your app**.



## What Is a Webhook?

A webhook works in the opposite direction.

Instead of your application requesting information, the **platform sends information to you automatically**.

Example with Shopify:

```
Customer places order
        ↓
Shopify creates order
        ↓
Shopify sends webhook to your server
```

Example request:

```http
POST https://yourapp.com/webhooks/orders-create
```

Payload:

```json
{
  "id": 12345,
  "total_price": "120.00"
}
```

Your server did **not ask for this data**.

Shopify sent it automatically.



## Another Example Using GitHub

Consider **GitHub**.

When a developer pushes new code:

```
Developer pushes code
        ↓
GitHub detects push event
        ↓
GitHub sends webhook
```

Example webhook request:

```http
POST /github-webhook
```

Payload example:

```json
{
  "ref": "refs/heads/main",
  "repository": {
    "name": "my-project"
  }
}
```

This might trigger:

* automated tests
* deployment
* Slack notifications

Again, your server **did not request the data**.

GitHub sent it because an event happened.



## Push vs Pull Communication

This difference is often explained using **push vs pull**.

| Communication Style | Meaning                       |
| ------------------- | ----------------------------- |
| Pull                | Client pulls data from server |
| Push                | Server pushes data to client  |

#### API = Pull

```
Client → Server → Request data
```

#### Webhook = Push

```
Server → Client → Send event data
```



## Visual Comparison

#### API Flow

```
Your App
   ↓
Request data
   ↓
Platform API
   ↓
Return response
```

#### Webhook Flow

```
Event happens
   ↓
Platform
   ↓
Webhook request sent
   ↓
Your server processes data
```



## When to Use APIs

APIs are best when your application needs to **actively request data**.

Examples:

* fetching product lists
* retrieving customer data
* loading analytics
* searching information

Example:

```http
GET /products
```

Your app decides when to call the API.



## When to Use Webhooks

Webhooks are best when you need to know **when something happens**.

Examples:

| Event                 | Platform |
| --------------------- | -------- |
| Order created         | Shopify  |
| Code pushed           | GitHub   |
| Payment succeeded     | Stripe   |
| Subscription canceled | Stripe   |

Instead of checking repeatedly, the platform simply **notifies you**.



## Real Systems Use Both

Most modern applications use **both APIs and webhooks together**.

Example using Shopify:

```
Webhook tells you: "Order created"
        ↓
Your app receives order ID
        ↓
Your app calls API to fetch full order details
```

Flow:

```
Webhook → Event notification
API → Retrieve detailed data
```

This pattern is very common.



## Quick Example Flow

```
Customer buys product
        ↓
Shopify webhook sent
        ↓
Your server receives order ID
        ↓
Your server calls Shopify API
        ↓
Fetch full order information
```

So webhooks often act as a **trigger** for API requests.



## Key Takeaway

The difference can be summarized like this:

| Feature       | API             | Webhook         |
| ------------- | --------------- | --------------- |
| Direction     | Client → Server | Server → Client |
| Purpose       | Request data    | Notify events   |
| Communication | Pull            | Push            |



## Mental Model

Whenever you hear **API**, think:

```
I request information
```

Whenever you hear **webhook**, think:

```
The system notifies me
```



## Quick Recap

API:

```
Your app asks for data
```

Webhook:

```
A platform automatically sends data
```

Both technologies work together to power modern integrations.



## Next Chapter

Now that you understand the difference between **APIs and webhooks**, we will walk through a **complete webhook lifecycle**.

In the next chapter you will learn:

* how a webhook is triggered
* how the request is sent
* how your server processes it
* what the full webhook flow looks like

This is where webhooks start to feel **like a real system instead of just a concept**.
