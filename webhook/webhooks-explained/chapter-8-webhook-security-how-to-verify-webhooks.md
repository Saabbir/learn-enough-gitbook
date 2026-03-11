# 📘 CHAPTER 8 — Webhook Security (How to Verify Webhooks)

So far in the series you learned how to:

* understand webhooks
* build a webhook server
* test webhooks locally

Now we must address a **critical real-world issue**.

> **How do you know a webhook actually came from the platform?**

Because technically **anyone could send a request to your webhook URL**.

For example, someone could send this fake request:

```http
POST https://yourapp.com/webhook
```

Payload:

```json
{
  "id": 999999,
  "total_price": "1000000"
}
```

If your server blindly trusts this data, it could cause serious problems.

Examples:

* fake orders
* fake payments
* fake deployments

To prevent this, platforms use **webhook signatures**.



## The Idea Behind Webhook Security

Platforms attach a **cryptographic signature** to every webhook request.

This signature proves that:

```
The webhook was sent by the real platform.
```

Your server must verify the signature before processing the request.



## How Signature Verification Works

The process looks like this:

```
Platform sends webhook
        ↓
Webhook includes signature
        ↓
Your server calculates expected signature
        ↓
Compare signatures
        ↓
If match → request is trusted
```

If the signatures do **not match**, the webhook should be rejected.



## Example — Shopify Webhook Signature

Platforms like **Shopify** include a header like this:

```http
X-Shopify-Hmac-Sha256: abc123signature
```

This header contains a **hash of the webhook payload**.

The hash is generated using a **shared secret** known only by:

* Shopify
* your application

This ensures that only Shopify can generate the correct signature.



## What Is HMAC?

The signature used by many platforms is called **HMAC**.

HMAC stands for:

```
Hash-based Message Authentication Code
```

It is a cryptographic technique used to verify:

* message authenticity
* message integrity

Most webhook systems use:

```
HMAC + SHA256
```

You don’t need to understand deep cryptography — you just need to verify the signature.



## Visual Security Flow

Webhook security works like this:

```
Platform
   ↓
Generate HMAC signature
   ↓
Send webhook + signature header
   ↓
Your server recalculates signature
   ↓
Compare signatures
   ↓
Accept or reject request
```



## Example Implementation (Node.js)

Here is a simplified example of verifying a Shopify webhook.

```javascript
const crypto = require("crypto")

function verifyWebhook(rawBody, signature, secret) {

  const hash = crypto
    .createHmac("sha256", secret)
    .update(rawBody)
    .digest("base64")

  return hash === signature
}
```

You would compare:

```javascript
expectedSignature === shopifySignature
```

If they match, the webhook is valid.



## Example Server Verification

Example webhook handler:

```javascript
const crypto = require("crypto")

app.post("/webhook", express.raw({ type: "*/*" }), (req, res) => {

  const secret = process.env.SHOPIFY_WEBHOOK_SECRET
  const signature = req.headers["x-shopify-hmac-sha256"]

  const hash = crypto
    .createHmac("sha256", secret)
    .update(req.body)
    .digest("base64")

  if (hash !== signature) {
    return res.status(401).send("Invalid webhook")
  }

  console.log("Webhook verified")

  res.status(200).send("OK")
})
```

Now your server will only accept **valid webhooks from Shopify**.



## Example — GitHub Webhook Signature

**GitHub** uses a similar approach.

Header example:

```http
X-Hub-Signature-256: sha256=abcdef123
```

GitHub signs the payload using your **webhook secret**.

Your server recalculates the hash and compares it.



## Example GitHub Verification

Simplified example:

```javascript
const crypto = require("crypto")

function verifyGitHubSignature(payload, signature, secret) {

  const hash = "sha256=" + crypto
    .createHmac("sha256", secret)
    .update(payload)
    .digest("hex")

  return hash === signature
}
```



## Why Verification Is Critical

Imagine a payment system using **Stripe**.

A webhook might say:

```json
{
 "event": "payment.succeeded",
 "amount": 500
}
```

Without verification, an attacker could send fake payment confirmations.

This could unlock paid content or services illegally.

Signature verification prevents this.



## Other Webhook Security Best Practices

Besides signature verification, production systems often use:

| Technique         | Purpose                          |
| ----------------- | -------------------------------- |
| HTTPS             | Encrypt requests                 |
| IP allowlist      | Accept requests from trusted IPs |
| Replay protection | Prevent duplicate attacks        |
| Rate limiting     | Protect against abuse            |

But **signature verification is the most important**.



## Mental Model

When receiving a webhook, always think:

```
1. Receive webhook
2. Verify signature
3. Process data
```

Never process webhook data **before verification**.



## Quick Recap

Webhook security ensures that:

```
Only trusted platforms can trigger your webhook.
```

Platforms include a **signature header**.

Examples:

| Platform | Signature Header      |
| -------- | --------------------- |
| Shopify  | X-Shopify-Hmac-Sha256 |
| GitHub   | X-Hub-Signature-256   |
| Stripe   | Stripe-Signature      |

Your server must:

```
Recalculate signature → Compare → Accept or reject
```



## Next Chapter

There is another important issue with webhooks in production.

Sometimes **the same webhook is delivered multiple times**.

Platforms retry if your server fails.

This can cause problems like:

* duplicate orders
* repeated database writes
* duplicated workflows

In the next chapter we will learn:

> **How to handle duplicate webhooks safely using idempotency.**
