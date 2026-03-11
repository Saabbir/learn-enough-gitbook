# 📘 CHAPTER 6 — Building Your First Webhook Server

So far in this series we learned:

* **Chapter 1:** The problem webhooks solve
* **Chapter 2:** What a webhook is
* **Chapter 3:** Webhooks vs APIs
* **Chapter 4:** How webhooks work
* **Chapter 5:** Anatomy of a webhook request

Now it's time to build something practical.

> In this chapter you will build a **real webhook server** that can receive webhook requests.

We will use **Node.js** with **Express** because it is simple and beginner-friendly.



## What We Are Building

We will create a server with one endpoint:

```
POST /webhook
```

When a webhook request arrives, the server will:

1. Receive the request
2. Read the JSON payload
3. Print the data to the console
4. Respond with `200 OK`

This is the **basic structure of every webhook handler**.



## Step 1 — Create a Project

Create a new folder.

```bash
mkdir webhook-server
cd webhook-server
```

Initialize a Node.js project:

```bash
npm init -y
```

This creates a `package.json` file.



## Step 2 — Install Express

Install **Express.js**.

```bash
npm install express
```

Express helps us create a web server quickly.



## Step 3 — Create the Server File

Create a file called:

```
server.js
```

Add this code:

```javascript
const express = require("express")

const app = express()

app.use(express.json())

app.post("/webhook", (req, res) => {

  console.log("Webhook received!")

  console.log("Headers:", req.headers)

  console.log("Payload:", req.body)

  res.status(200).send("Webhook received")

})

app.listen(3000, () => {
  console.log("Server running on port 3000")
})
```



## Step 4 — Run the Server

Start the server.

```bash
node server.js
```

You should see:

```
Server running on port 3000
```

Your webhook server is now listening for requests.



## Step 5 — Send a Test Webhook

We can simulate a webhook request using `curl`.

Run this command in a new terminal:

```bash
curl -X POST http://localhost:3000/webhook \
-H "Content-Type: application/json" \
-d '{"event":"order.created"}'
```



## What Happens Next

Your server receives the request and prints the data.

Example output:

```
Webhook received!

Headers: { ... }

Payload: { event: 'order.created' }
```

Your webhook endpoint is working 🎉



## Understanding the Code

Let's break down the important parts.

### Creating the Server

```javascript
const express = require("express")
const app = express()
```

This creates a new Express application.

### Parsing JSON

```javascript
app.use(express.json())
```

Most webhook payloads are **JSON**.

This line tells Express to automatically parse JSON bodies.

### Creating the Webhook Endpoint

```javascript
app.post("/webhook", handler)
```

This creates an endpoint that accepts:

```
POST /webhook
```

Which is exactly how most webhook requests arrive.

### Accessing Webhook Data

Inside the handler:

```javascript
req.headers
req.body
```

These contain:

| Property    | Contains                   |
| ----------- | -------------------------- |
| req.headers | metadata about the webhook |
| req.body    | event payload              |

### Sending a Response

```javascript
res.status(200).send("Webhook received")
```

Platforms expect a success response.

If you return:

```
200 OK
```

The platform knows the webhook was processed.

If you return an error or timeout, the platform may retry.



## Example with Shopify Webhook

If **Shopify** sends an order webhook:

```json
{
 "id": 938274,
 "email": "customer@email.com",
 "total_price": "120.00"
}
```

Your server receives it like this:

```javascript
const order = req.body

console.log(order.id)
console.log(order.total_price)
```

You could then:

* save order to database
* send order to warehouse
* trigger analytics event



## Example with GitHub Webhook

If **GitHub** sends a push event:

```json
{
 "ref": "refs/heads/main",
 "repository": {
   "name": "my-project"
 }
}
```

Your server could trigger a CI pipeline.

Example:

```javascript
if (req.body.ref === "refs/heads/main") {
  console.log("Main branch updated")
}
```



## Visual Flow of Your Server

Your server now participates in the webhook pipeline.

```
Platform
   ↓
Webhook POST request
   ↓
Your server (/webhook)
   ↓
Process event data
   ↓
Return 200 OK
```

This pattern powers **millions of integrations**.



## Important Beginner Tip

Webhook endpoints should **respond quickly**.

Do not run heavy operations before returning `200 OK`.

Instead:

```
Receive webhook
↓
Respond quickly
↓
Process event asynchronously
```

We will discuss this more later in the series.



## Quick Recap

In this chapter you learned how to:

* create a webhook server
* build a webhook endpoint
* receive webhook payloads
* inspect headers and body
* return a success response

You now have a **working webhook listener**.



## Next Chapter

In real development, the next challenge appears immediately:

> **How do we test webhooks from real platforms?**

Your local computer cannot receive webhooks from services like:

* Shopify
* GitHub

because your local server is not publicly accessible.

In the next chapter you will learn:

* how to expose your local server to the internet
* how to test webhooks from real platforms
* how to debug webhook requests

We will introduce tools like **ngrok** that every webhook developer uses.
