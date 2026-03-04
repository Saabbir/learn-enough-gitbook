# 📘 ARTICLE 02 — Introduction to Google Analytics

In **Article 01**, we learned how website tracking works and how tracking scripts collect user interactions.

Now we will explore the most widely used analytics platform in the world:

**Google Analytics.**

Understanding Google Analytics is essential before learning about **Google Tag Manager, Shopify tracking architecture, and tag auditing**.



## 1. What is Google Analytics?

Google Analytics is a **web analytics platform** used to measure and analyze website traffic.

It helps businesses understand:

* how many people visit the website
* where visitors come from
* what pages users view
* how users interact with the site
* which marketing campaigns generate sales

Instead of guessing what users do on a website, Google Analytics provides **data-driven insights**.



## 2. What Data Google Analytics Collects

Google Analytics collects multiple types of data.

### 1. User Data

Information about visitors.

Examples:

```
new users
returning users
country
device type
browser
operating system
```

This helps businesses understand **who their visitors are**.

### 2. Traffic Source Data

Google Analytics tracks where visitors come from.

Examples:

```
Google search
Facebook ads
Email campaigns
Direct visits
Referral websites
```

Example report:

```
Organic Search → 40%
Paid Ads → 30%
Direct Traffic → 20%
Social Media → 10%
```

This helps businesses understand **which marketing channels perform best**.

### 3. Behavioral Data

This shows what users do on the website.

Examples:

```
pages viewed
time spent on page
scroll depth
button clicks
navigation paths
```

This helps businesses identify **user experience issues**.

### 4. Ecommerce Data

For online stores like Shopify, Google Analytics tracks ecommerce interactions.

Examples:

```
product views
add to cart
checkout start
purchase
order value
revenue
```

This helps measure **sales performance and conversion rates**.



## 3. How Google Analytics Collects Data

Google Analytics collects data using **tracking scripts installed on a website**.

When a user visits the website:

```
User loads webpage
↓
Google Analytics tracking script loads
↓
User performs action (page view, click, purchase)
↓
Script records event
↓
Event sent to Google Analytics servers
↓
Data appears in analytics reports
```

Example:

```
User opens product page
↓
page_view event sent to GA4
↓
User clicks add to cart
↓
add_to_cart event sent to GA4
↓
User completes purchase
↓
purchase event sent to GA4
```



## 4. Google Analytics Properties

A **property** is the container that stores your website's analytics data.

Example:

```
Business
└── Google Analytics Account
     └── Property
           └── Website Data
```

Each property receives data from **one or more websites or apps**.



## 5. GA4 Measurement ID

Each Google Analytics property has a unique **Measurement ID**.

Format:

```
G-XXXXXXXXXX
```

Example:

```
G-9V5CXZEBCN
```

This ID tells the tracking script:

```
Send analytics data to this specific GA property
```



## 6. Example Google Analytics Script

Example GA4 installation:

```
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
```

Configuration code:

```
gtag('config', 'G-XXXXXXXXXX');
```

This script initializes Google Analytics and starts sending data.



## 7. Core Concepts in Google Analytics

Understanding these terms is essential for auditing analytics implementations.

### Users

Users represent **unique visitors** to the website.

Example:

```
1000 users visited the site today
```

A user may visit multiple pages during one session.

### Sessions

A session represents **one visit to the website**.

Example:

```
User visits homepage
→ browses products
→ leaves website
```

This counts as **one session**.

If the user returns later, it becomes **another session**.

### Events

Events represent **specific actions taken by users**.

Examples:

```
page_view
view_item
add_to_cart
begin_checkout
purchase
```

Events are the foundation of **modern analytics tracking**.

### Conversions

Conversions are **important events marked as business goals**.

Examples:

```
purchase
lead form submission
newsletter signup
```

Businesses use conversions to measure **success metrics**.



## 8. Ecommerce Tracking in Google Analytics

For Shopify stores, e-commerce tracking is critical.

Google Analytics records key e-commerce events.

### view\_item

Triggered when a product page is viewed.

Example data:

```
product name
product ID
price
category
```

### add\_to\_cart

Triggered when a user adds a product to the cart.

### begin\_checkout

Triggered when checkout begins.

### purchase

Triggered when the order is completed.

Example purchase data:

```
transaction_id
value
currency
items purchased
```

This data powers **revenue and sales reports**.



## 9. Why Google Analytics Data Can Be Wrong

Incorrect analytics implementations can produce misleading data.

Common problems include:

### Duplicate Tracking

Example:

```
GA4 installed via GTM
+
GA4 installed via Shopify app
```

Result:

```
duplicate pageviews
duplicate purchases
inflated revenue
```

### Missing Events

Example:

```
purchase event not firing
```

Result:

```
revenue not recorded
conversion rate incorrect
```

### Incorrect Event Data

Example:

```
wrong currency
missing transaction ID
incorrect product values
```

This leads to **broken e-commerce reports**.



## 10. Why GA4 Replaced Universal Analytics

Before GA4, Google used **Universal Analytics (UA)**.

UA tracking ID format:

```
UA-12345678-1
```

UA used a **session-based model**.

GA4 uses an **event-based model**, which is more flexible.

Benefits of GA4:

```
Better cross-device tracking
Improved machine learning insights
Flexible event tracking
Better privacy compliance
```

Universal Analytics was officially **deprecated in 2023**.

GA4 is now the **standard analytics platform**.



## 11. Where Google Analytics Is Installed

Google Analytics can be installed in several ways.

Common methods include:

### Direct Script Installation

Installed directly in website code.

Example:

```
gtag.js
```

### Google Tag Manager

Installed via GTM container.

Example:

```
GTM loads → GA4 tag fires
```

### Shopify Apps

Shopify apps can automatically install analytics tags.

Example:

```
Google & YouTube Shopify app
```

This is a common cause of **duplicate analytics tracking**.



## Key Takeaways

Important concepts from this article:

* Google Analytics measures website traffic and user behavior.
* GA4 uses **event-based tracking**.
* Every GA4 property has a **Measurement ID (G-XXXXXXXX)**.
* GA4 tracks events such as page views, product views, and purchases.
* Incorrect implementations can lead to inaccurate data.
* GA4 replaced Universal Analytics in 2023.



## Next Article

### 📘 ARTICLE 03 — GA4 vs Universal Analytics Explained

In the next article, we will explore:

* The history of Google Analytics
* The differences between GA4 and Universal Analytics
* Session-based vs event-based tracking
* Why GA4 fundamentally changed analytics architecture
* What auditors must know about legacy tracking setups

This knowledge will help you understand **old tracking setups still found on many websites today** before we move to **Google Tag Manager in the next chapters**.
