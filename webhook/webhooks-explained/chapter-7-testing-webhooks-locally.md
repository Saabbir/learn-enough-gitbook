# 📘 CHAPTER 7 — Testing Webhooks Locally

In the previous chapter you built your **first webhook server**.

Your server can now receive requests at:

```
POST http://localhost:3000/webhook
```

But there is a problem.

Platforms like:

* Shopify
* GitHub
* Stripe

cannot send webhooks to **localhost**.

Why?

Because your local machine is **not publicly accessible on the internet**.

So we need a way to temporarily expose your local server.



## The Problem with Local Servers

When you run your server locally:

```bash
node server.js
```

Your server is available at:

```
http://localhost:3000
```

or

```
http://127.0.0.1:3000
```

But this address only works **inside your own computer**.

External services cannot reach it.



## The Solution — Tunneling

To test webhooks locally, developers use a **tunneling tool**.

A tunneling tool creates a **temporary public URL** that forwards traffic to your local server.

Flow:

```
Internet
   ↓
Public URL
   ↓
Tunnel
   ↓
Your local server
```

One of the most popular tools is **ngrok**.



## Installing ngrok

First install **ngrok**.

Download it from:

```
https://ngrok.com
```

Or install using Homebrew (Mac):

```bash
brew install ngrok
```

After installing, you can run the command:

```bash
ngrok http 3000
```



## Running ngrok

Assuming your webhook server runs on port **3000**, start ngrok:

```bash
ngrok http 3000
```

Example output:

```
Forwarding https://abc123.ngrok.io -> http://localhost:3000
```

You now have a **public URL**:

```
https://abc123.ngrok.io
```

Requests sent to this URL will be forwarded to:

```
http://localhost:3000
```



## Your Webhook Endpoint with ngrok

Your webhook endpoint becomes:

```
https://abc123.ngrok.io/webhook
```

Now external platforms can send requests to your local server.



## Testing with curl

Before connecting a real platform, test it manually.

Example:

```bash
curl -X POST https://abc123.ngrok.io/webhook \
-H "Content-Type: application/json" \
-d '{"event":"test.webhook"}'
```

Your local server should print:

```
Webhook received!
Payload: { event: 'test.webhook' }
```

This confirms your tunnel is working.



## Testing a GitHub Webhook

Now let's test a real webhook using **GitHub**.

Steps:

1. Go to your repository
2. Open **Settings**
3. Click **Webhooks**
4. Click **Add webhook**



## GitHub Webhook Configuration

Fill in the settings.

**Payload URL**

```
https://abc123.ngrok.io/webhook
```

**Content type**

```
application/json
```

**Events**

Choose:

```
Just the push event
```

Save the webhook.



## Trigger the Webhook

Now push code to your repository.

Example:

```bash
git commit -m "test webhook"
git push
```

GitHub detects the push and sends a webhook.

Your server console should show something like:

```
Webhook received!
Payload: { ref: 'refs/heads/main', repository: {...} }
```

Your webhook listener is now connected to GitHub.



## Testing Shopify Webhooks

You can also test using **Shopify**.

In the Shopify admin panel:

```
Settings → Notifications → Webhooks
```

Create a webhook.

Example settings:

Event:

```
Order creation
```

URL:

```
https://abc123.ngrok.io/webhook
```

Whenever a new order is created, Shopify will send the webhook.

Your server receives the order data instantly.



## Viewing Webhook Requests

One great feature of **ngrok** is the inspection dashboard.

Open:

```
http://localhost:4040
```

This shows:

* all incoming requests
* headers
* payloads
* response status

This is extremely useful for debugging.



## Debugging Webhooks

When a webhook fails, check:

| Issue                     | Solution                    |
| ------------------------- | --------------------------- |
| Server not running        | Start your Node server      |
| Wrong endpoint URL        | Verify webhook path         |
| Timeouts                  | Respond quickly with 200    |
| Incorrect payload parsing | Ensure JSON parsing enabled |

Debugging webhooks often involves inspecting:

```javascript
console.log(req.headers)
console.log(req.body)
```



## Visual Flow of Local Webhook Testing

Your setup now looks like this:

```
Platform (GitHub / Shopify)
        ↓
Public URL (ngrok)
        ↓
Tunnel
        ↓
Local server (Node.js)
```

This setup is used by **developers worldwide** when building integrations.



## Important Developer Tip

Ngrok URLs are **temporary**.

Every time you restart ngrok, the URL changes.

Example:

```
https://abc123.ngrok.io
```

may become:

```
https://xyz987.ngrok.io
```

So you may need to update the webhook URL.



## Quick Recap

In this chapter you learned how to:

* expose your local server to the internet
* create a public webhook endpoint
* test webhooks from real platforms
* debug webhook requests

Tools introduced:

* ngrok

Platforms tested:

* GitHub
* Shopify

You now have a **fully working webhook development setup**.



## Next Chapter

Now we will address a **critical production concern**:

> **Webhook security**

What prevents someone from sending **fake webhooks** to your server?

In the next chapter you will learn:

* why webhook security matters
* how platforms sign webhook requests
* how to verify webhook authenticity

This is essential for platforms like:

* Shopify
* Stripe

where fake webhooks could cause serious problems.
