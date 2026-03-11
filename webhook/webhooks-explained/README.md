# 📚 Webhooks Explained

## **Goal of the series**

By the end, a beginner will be able to:

* Understand what webhooks are
* Understand how platforms use them
* Build a webhook listener
* Test webhooks locally
* Understand security and reliability basics

We will follow the **80/20 rule,** focusing on the knowledge that developers actually use.



## Chapter 1 — The Problem Webhooks Solve

Topics:

* Why systems need notifications
* The polling problem
* Inefficiency of repeated API requests
* Real-world analogy

Key takeaway:

```
Polling = constantly asking for updates
Webhooks = server tells you when something happens
```

No platform examples yet.\
Just the **core problem**.



## Chapter 2 — What a Webhook Actually Is

Now we define the concept.

Definition:

> A webhook is an **HTTP request sent automatically when an event occurs**.

Core idea:

```
EVENT → HTTP POST → YOUR SERVER
```

Simple example:

```
POST https://example.com/webhook
```

Payload example:

```json
{
 "event": "order.created",
 "order_id": 12345
}
```

Key takeaway:

A webhook is simply **an automated HTTP request triggered by an event**.



## Chapter 3 — API Calls vs Webhooks

This chapter clears the biggest confusion beginners have.

| Feature              | API                 | Webhook           |
| -------------------- | ------------------- | ----------------- |
| Who starts request   | Client              | Server            |
| When request happens | When client decides | When event occurs |
| Communication style  | Pull                | Push              |

Diagram:

```
API
Client → Server

Webhook
Server → Client
```

This is where the concept **finally clicks** for beginners.



## Chapter 4 — Real Example: Shopify Webhook

Now we introduce the first **real-world system**.

Scenario:

Customer places an order.

Flow:

```
Customer buys product
        ↓
Shopify creates order
        ↓
Shopify sends webhook
        ↓
Your server receives order data
```

Example request:

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

What developers do with this:

* send order to **3PL**
* store order in **database**
* send **Slack notification**
* update **analytics**

This shows beginners **why webhooks matter**.



## Chapter 5 — Another Example: GitHub Webhook

Now we show webhooks outside ecommerce.

Scenario:

Developer pushes code.

Flow:

```
Developer pushes code
        ↓
GitHub detects push
        ↓
GitHub sends webhook
        ↓
CI server runs tests
```

Example request:

```
POST /github-webhook
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

What systems do with this:

* trigger **CI/CD**
* run **tests**
* deploy servers
* notify **Slack**

Now the learner understands:

> Webhooks power **automation everywhere**.



## Chapter 6 — Anatomy of a Webhook Request

Now we break down what a webhook request actually contains.

Every webhook has four parts.

#### 1️⃣ URL

Where the request is sent.

```
https://yourserver.com/webhook
```

#### 2️⃣ HTTP Method

Usually:

```
POST
```

#### 3️⃣ Headers

Example:

```
Content-Type: application/json
X-Shopify-Topic: orders/create
```

Headers often include **security signatures**.

#### 4️⃣ Payload

The event data.

Example:

```json
{
 "order_id": 12345,
 "price": 199
}
```



## Chapter 7 — Building Your First Webhook Server

Now we finally write code.

Example Node.js webhook listener.

Install Express:

```
npm install express
```

Server:

```javascript
const express = require('express')
const app = express()

app.use(express.json())

app.post('/webhook', (req, res) => {

  console.log("Webhook received")

  console.log(req.body)

  res.status(200).send("OK")

})

app.listen(3000, () => {
  console.log("Server running")
})
```

Now the learner has built a **real webhook endpoint**.



## Chapter 8 — Testing Webhooks Locally

Beginners often struggle here.

We introduce tools:

| Tool         | Purpose             |
| ------------ | ------------------- |
| ngrok        | expose local server |
| webhook.site | inspect requests    |
| Postman      | send test webhooks  |

Example using curl:

```
curl -X POST http://localhost:3000/webhook \
-H "Content-Type: application/json" \
-d '{"event":"test"}'
```



## Chapter 9 — Webhook Security (Beginner Level)

Problem:

Anyone could send fake webhooks.

Solution:

Platforms send **verification signatures**.

Example:

```
X-Shopify-Hmac-Sha256
```

Server verifies the signature before trusting the request.

Concept only — not deep crypto.



## Chapter 10 — Handling Retries and Duplicates

Important real-world behavior.

Webhook providers retry if your server fails.

Example:

```
Shopify sends webhook
↓
Server fails
↓
Shopify retries
```

This can cause **duplicate events**.

Solution:

Use **idempotency**.

Example:

```
Check if order already processed
```



## Chapter 11 — Real Webhook Architecture

Production systems often use queues.

```
Webhook
   ↓
Queue (Redis / SQS)
   ↓
Worker
   ↓
Database
```

Why?

Because webhook endpoints must respond **very quickly**.



## Chapter 12 — Where Webhooks Are Used Everywhere

Examples:

| Platform | Event           |
| -------- | --------------- |
| Shopify  | order created   |
| GitHub   | push            |
| Stripe   | payment success |
| Slack    | message posted  |
| Discord  | bot events      |

Key idea:

> Webhooks are the backbone of modern integrations.



## Why This Series Works

This structure follows **beginner learning psychology**:

```
Problem
↓
Concept
↓
Comparison
↓
Real examples
↓
Implementation
↓
Production concerns
```

Each chapter introduces **only one new idea**.
