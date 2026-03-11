# 📘 CHAPTER 5 — Anatomy of a Webhook Request

In the previous chapter we learned **how webhooks work step-by-step**.

Now we will zoom in and examine **what a webhook request actually looks like**.

Understanding this is important because when you work with webhooks, you will often need to:

* inspect webhook requests
* debug webhook payloads
* read event data
* verify webhook security

Every webhook request contains **four main parts**.



## The 4 Parts of a Webhook Request

Most webhooks contain these components:

| Part           | Purpose                    |
| -------------- | -------------------------- |
| URL            | Where the webhook is sent  |
| HTTP Method    | Usually `POST`             |
| Headers        | Metadata about the request |
| Body (Payload) | The event data             |

Think of it like a package being delivered.

```
Webhook Request
   ├── URL (destination)
   ├── Method
   ├── Headers
   └── Payload
```

Let's examine each part.



## 1 — Webhook URL

The **URL** is the address where the webhook request is delivered.

Example:

```
https://yourapp.com/webhooks/orders-create
```

This means the platform will send the webhook to this endpoint.

Your server must have a route ready to receive it.

Example server endpoint:

```javascript
app.post('/webhooks/orders-create', handler)
```

Whenever the webhook arrives, this endpoint handles it.



## 2 — HTTP Method

Most webhook systems use the **POST** method.

Example request:

```http
POST /webhooks/orders-create
```

Why POST?

Because webhooks **send data** to your server.

POST requests allow sending a request body.

Occasionally you might see:

* `PUT`
* `PATCH`

But **POST is by far the most common**.



## 3 — Webhook Headers

Headers contain **metadata about the webhook request**.

They help your server understand things like:

* event type
* request format
* authentication
* signature verification

Example headers from **Shopify**:

```http
Content-Type: application/json
X-Shopify-Topic: orders/create
X-Shopify-Shop-Domain: example-store.myshopify.com
X-Shopify-Hmac-Sha256: abc123signature
```

These headers tell your server:

* what event triggered the webhook
* which store sent it
* how to verify the request

We will cover **security verification later in the series**.



## 4 — Webhook Payload (Body)

The payload contains the **actual event data**.

This is usually sent as **JSON**.

Example payload from Shopify:

```json
{
  "id": 938274,
  "email": "customer@email.com",
  "total_price": "120.00",
  "currency": "USD"
}
```

Your server can read this data and decide what to do.

Example:

```javascript
app.post('/webhook', (req, res) => {

  const order = req.body

  console.log(order.id)
  console.log(order.total_price)

  res.status(200).send("OK")

})
```



## Example — Full Webhook Request

Let’s combine everything together.

Example webhook request from **Shopify**.

```http
POST /webhooks/orders-create HTTP/1.1
Host: yourapp.com
Content-Type: application/json
X-Shopify-Topic: orders/create

{
  "id": 938274,
  "email": "customer@email.com",
  "total_price": "120.00"
}
```

Breakdown:

| Part    | Value                          |
| ------- | ------------------------------ |
| Method  | POST                           |
| URL     | /webhooks/orders-create        |
| Headers | Content-Type, Shopify metadata |
| Payload | order data                     |



## Another Example — GitHub Webhook

Now let's look at an example from **GitHub**.

Example webhook request:

```http
POST /github-webhook
Content-Type: application/json
X-GitHub-Event: push
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

This payload tells your system:

* which branch was pushed
* which repository changed
* who pushed the code

Your server might then trigger:

* CI pipelines
* automated tests
* deployment



## Visual Structure of a Webhook Request

Here is the simplified structure:

```
HTTP POST /webhook-url
│
├── Headers
│     ├── Content-Type
│     ├── Event type
│     └── Security signature
│
└── Body
      └── JSON payload (event data)
```

This structure is used by **most webhook providers**.



## Important Developer Tip

When working with webhooks, you will often debug by inspecting:

* request headers
* JSON payload
* event type

For example:

```javascript
console.log(req.headers)
console.log(req.body)
```

This helps you understand exactly what the platform sent.



## Mental Model

Whenever you receive a webhook request, imagine it as:

```
Event happened
      ↓
Platform sends POST request
      ↓
Headers describe the event
      ↓
Body contains event data
```



## Quick Recap

A webhook request contains:

| Component | Description                    |
| --------- | ------------------------------ |
| URL       | Where the webhook is delivered |
| Method    | Usually POST                   |
| Headers   | Metadata and security info     |
| Payload   | Event data (usually JSON)      |

This structure is used by platforms like:

* Shopify
* GitHub
* Stripe



## Next Chapter

So far we have only **looked at webhook requests**.

Now it’s time to **build one ourselves**.

In the next chapter you will learn how to:

* create a webhook endpoint
* build a simple webhook server
* receive webhook requests
* inspect incoming payloads

We will build our **first webhook listener using Node.js**. 🚀
