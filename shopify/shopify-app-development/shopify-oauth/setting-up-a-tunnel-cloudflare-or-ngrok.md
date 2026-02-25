---
description: Without it, OAuth can’t complete end-to-end.
---

# Setting Up a Tunnel (Cloudflare or ngrok)

## Goal of this article

By the end, you will:

* Expose your local server to the internet
* Get a public HTTPS URL
* Update `.env`
* Update Shopify **Dev Dashboard** correctly
* Successfully open `/install` from Shopify

No Shopify code changes yet — only infrastructure.



## Why You Need a Tunnel (Quick Reminder)

Shopify:

* Runs on Shopify servers
* Must send redirects + webhooks to **your backend**

Your backend:

* Is running on `localhost`
* Is invisible to the internet ❌

A tunnel creates this bridge:

```
Shopify → Public URL → Your localhost server
```



## ✅ OPTION A: Cloudflare Tunnel (Free, Stable)

This is **Shopify-friendly**, no credit card, no random shutdowns.



### 🔹 STEP 1 — Install cloudflared

#### macOS

```bash
brew install cloudflare/cloudflare/cloudflared
```

#### Linux

```bash
curl -L https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb -o cloudflared.deb
sudo dpkg -i cloudflared.deb
```

#### Windows

Download from:\
[https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation)



### 🔹 STEP 2 — Start the tunnel

Make sure your app is running:

```bash
node app.js
```

In a **new terminal tab**, run:

```bash
cloudflared tunnel --url http://localhost:3000
```

You’ll see output like:

```txt
https://smooth-moon-1234.trycloudflare.com
```

🔥 **THIS is your app’s public URL**

Copy it.



### 🔹 STEP 3 — Update `.env`

Open `.env` and update:

```env
APP_URL=https://smooth-moon-1234.trycloudflare.com
```

⚠️ No trailing slash\
⚠️ Must be HTTPS

Restart your server:

```bash
CTRL + C
node app.js
```



### 🔹 STEP 4 — Update Shopify Dev Dashboard (Critical)

Go to your app in **Dev Dashboard**:

#### 📍 Settings → Configuration

Set:

**App URL**

```txt
https://smooth-moon-1234.trycloudflare.com
```

**Allowed redirection URLs**

```txt
https://smooth-moon-1234.trycloudflare.com/auth/callback
```

Save changes.

✅ This is mandatory\
❌ OAuth will fail without this



### 🔹 STEP 5 — Test Manually (Important)

Open browser:

```
https://smooth-moon-1234.trycloudflare.com/
```

You should see:

```
Shopify app server running
```

If yes → tunnel works.



### 🔹 STEP 6 — Test OAuth Start

Now test:

```
https://smooth-moon-1234.trycloudflare.com/install?shop=YOUR-DEV-STORE.myshopify.com
```

Expected flow:

1. Redirects to Shopify
2. Shows permission screen
3. Click **Install**
4. Redirects to `/auth/callback`
5. Shows “🎉 App installed successfully”

If this works → **YOU DID IT**.



## 🟡 OPTION B: ngrok (Alternative)

> Official macOS installation guide: [https://ngrok.com/download/mac-os](https://ngrok.com/download/mac-os)

#### Install ngrok on macOS

```bash
brew install ngrok
```

#### Authenticate (required now):

```bash
ngrok config add-authtoken YOUR_TOKEN
```

Don’t have an authtoken? [Sign up](https://ngrok.com/signup?ref=downloads) for a free account.

#### Start tunnel:

```bash
ngrok http 3000
```

Copy HTTPS forwarding URL and use it exactly like Cloudflare.



⚠️ ngrok URLs change often on free plan.

✅ But you can claim your free ngrok dev domain that doesn't change.



> Calim your free ngrok dev domain: [https://dashboard.ngrok.com/domains](https://dashboard.ngrok.com/domains)
>
> Mine is [https://starling-needed-terminally.ngrok-free.app](https://starling-needed-terminally.ngrok-free.app)



## Common Issues & Fixes

#### ❌ “redirect\_uri is not whitelisted”

* URL mismatch
* Missing `/auth/callback`
* HTTP instead of HTTPS

***

#### ❌ Cookie state mismatch

* Restart server after changing `APP_URL`
* Clear browser cookies
* Use same tunnel URL consistently

***

#### ❌ HMAC failed

* Wrong `SHOPIFY_API_SECRET`
* Query params modified
* Using old callback URL



## Final Checklist (Verify All)

✔ Server running on `localhost:3000`\
✔ Tunnel running\
✔ `APP_URL` updated\
✔ Shopify App URL updated\
✔ Redirect URL updated\
✔ `/install` works\
✔ `/auth/callback` completes

If all checked → you’re **fully wired**.



## What You’ve Achieved (Big)

At this point:

* You have a real Shopify app
* OAuth works end-to-end
* Tokens are stored
* Backend is production-structured
* Infra is correctly set up



## ▶️ Next Article — Calling Shopify Admin API (Your First Real API Call)

Next we’ll:

* Use stored access tokens
* Call Shopify Admin REST API
* Fetch products
* Handle headers & versions
* Build `/api/products`
