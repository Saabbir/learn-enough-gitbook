# 📘 CHAPTER 4 — How Webhooks Work (Step-by-Step)

In the previous chapters we learned:

* **Chapter 1:** The problem webhooks solve (polling)
* **Chapter 2:** What a webhook is
* **Chapter 3:** The difference between **APIs vs Webhooks**

Now we will answer a practical question:

> **What actually happens when a webhook is triggered?**

Understanding this lifecycle will make webhooks much easier to implement later.



## The Webhook Lifecycle

A webhook follows a predictable sequence of steps.

```
1. Event happens
2. Platform detects event
3. Platform sends HTTP request
4. Your server receives webhook
5. Your server processes the data
6. Your server returns response
```

Let’s walk through each step.



## Step 1 — An Event Happens

Everything begins with an **event**.

An event is simply **something that occurred inside a system**.

Examples:

| Platform | Example Event            |
| -------- | ------------------------ |
| Shopify  | Customer places an order |
| GitHub   | Developer pushes code    |
| Stripe   | Payment succeeds         |

Example event:

```
order.created
```

or

```
push
```

The platform detects that this event happened.



## Step 2 — Platform Detects the Event

When the platform detects the event, it checks:

> “Are there any webhooks subscribed to this event?”

Example:

Your app may have registered a webhook like:

```
orders/create
```

When that event happens, the platform prepares to send a notification.



## Step 3 — Platform Sends the Webhook Request

Now the platform sends an **HTTP POST request** to your server.

Example webhook request:

```http
POST https://yourapp.com/webhooks/orders-create
```

The request includes event data.

Example payload:

```json
{
  "id": 123456,
  "email": "customer@email.com",
  "total_price": "120.00"
}
```

At this point the webhook has been delivered to your application.



## Step 4 — Your Server Receives the Webhook

Your server has an endpoint waiting for this request.

Example:

```
POST /webhooks/orders-create
```

When the webhook arrives, your application receives the data.

Example Node.js handler:

```javascript
app.post('/webhook', (req, res) => {

  console.log("Webhook received")

  console.log(req.body)

  res.status(200).send("OK")

})
```

Your code now has access to the webhook data.



## Step 5 — Your Server Processes the Event

After receiving the webhook, your application decides what to do.

Example actions:

| Action           | Example                 |
| ---------------- | ----------------------- |
| Save data        | Store order in database |
| Trigger workflow | Send order to 3PL       |
| Notify team      | Send Slack message      |
| Run automation   | Trigger deployment      |

Example:

```
Webhook received
      ↓
Save order to database
```

Or:

```
Webhook received
      ↓
Trigger CI pipeline
```



## Step 6 — Your Server Responds

Once your server receives the webhook, it must send a response.

Example:

```http
200 OK
```

This tells the platform:

> “Webhook received successfully.”

If your server fails to respond properly, the platform may retry the webhook.

We will discuss retries later in the series.



## Full Webhook Flow Example (Shopify)

Let’s put everything together using **Shopify**.

```
Customer buys product
        ↓
Shopify creates order
        ↓
Shopify detects event
        ↓
Shopify sends webhook request
        ↓
Your server receives webhook
        ↓
Your server processes order
        ↓
Server returns 200 OK
```

This entire process often happens in **milliseconds**.



## Another Example (GitHub)

Now the same lifecycle using **GitHub**.

```
Developer pushes code
        ↓
GitHub detects push event
        ↓
GitHub sends webhook
        ↓
Your server receives webhook
        ↓
CI/CD pipeline starts
        ↓
Server returns 200 OK
```

This is how many **automated deployment systems** work.



## Visual Webhook Flow

A simplified webhook diagram looks like this:

```
Event Occurs
     ↓
Platform Detects Event
     ↓
Webhook Sent (HTTP POST)
     ↓
Your Server Endpoint
     ↓
Process Event Data
     ↓
Return Response
```

This is the **core architecture of webhooks**.



## Important Thing to Remember

Webhooks are **one-way communication**.

```
Platform → Your Server
```

Your server does **not initiate the request**.

The platform sends the data when an event happens.



## Mental Model

Whenever you hear the word **webhook**, imagine this pipeline:

```
Event
  ↓
Webhook Request
  ↓
Server Processing
```

That’s the entire idea.



## Quick Recap

A webhook follows this lifecycle:

```
1. Event happens
2. Platform detects event
3. Webhook request sent
4. Your server receives request
5. Your code processes data
6. Server responds with 200 OK
```

This sequence is the foundation of **every webhook system**.



## Next Chapter

Now that you understand **how webhooks work**, the next step is to understand **what a webhook request actually looks like**.

In the next chapter we will break down:

* webhook URLs
* HTTP methods
* headers
* webhook payloads

This will help you **inspect and debug real webhooks** like a developer.
