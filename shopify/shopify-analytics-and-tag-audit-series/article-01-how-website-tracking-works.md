# 📘 ARTICLE 01 — How Website Tracking Works

This is the **first article in the Shopify Analytics & Tag Audit Series**.\
Before learning tools like **Google Analytics or Google Tag Manager**, it’s important to understand the **fundamental mechanics of website tracking**.

Everything else in analytics, events, conversions, attribution, and reports is built on this foundation.

If you understand this article well, the rest of the series will become **much easier to understand**.



## 1. Why Website Tracking Exists

Websites collect behavioral data to understand how users interact with them.

Businesses want to answer questions like:

* How many visitors come to the website?
* Where do visitors come from?
* Which pages do users visit?
* Which marketing channels generate sales?
* Where do users drop off before purchasing?

Without tracking, a website owner would only know **how many orders were placed**, but not:

* how customers found the site
* what they clicked before buying
* what marketing campaigns worked

Tracking allows businesses to measure and optimize **marketing, product, and user experience**.



## 2. What is Website Tracking?

Website tracking is the process of **collecting data about user interactions on a website**.

This is done by adding **tracking scripts** to the website.

These scripts detect user actions and send data to analytics platforms.

Examples of user interactions that are tracked:

```
Page view
Product view
Add to cart
Checkout start
Purchase
Button click
Scroll depth
Form submission
```

These interactions are called **events**.



## 3. The Basic Tracking Flow

At a high level, website tracking follows this process:

```
User visits website
↓
Tracking script loads in browser
↓
User performs an action
↓
Script detects the action
↓
Event is sent to analytics platform
↓
Analytics platform stores the data
```

Example:

```
User visits Shopify store
↓
Google Tag Manager loads
↓
GA4 tracking tag fires
↓
Page_view event sent to Google Analytics
```

This entire process happens in **milliseconds**.



## 4. What is a Tracking Tag?

A **tracking tag** is a piece of JavaScript code added to a website to collect data.

Tags send information about user actions to external platforms.

Common platforms include:

* analytics tools
* advertising platforms
* marketing tools
* customer data platforms

Examples of tags:

```
Google Analytics
Google Ads Conversion Tracking
Meta Pixel
TikTok Pixel
Hotjar
```

Each tag is responsible for sending specific data to its platform.



## 5. What Data Does a Tag Collect?

Tracking tags collect information about:

#### User actions

Examples:

```
page_view
product_view
add_to_cart
purchase
```

#### Page information

Examples:

```
page URL
page title
referrer URL
```

#### Ecommerce data

Examples:

```
product name
product price
currency
order value
transaction id
```

#### Device & browser information

Examples:

```
browser type
screen resolution
device type
operating system
```

This data helps businesses understand **who their users are and how they behave**.



## 6. What is an Event?

Modern analytics systems track **events** instead of simple pageviews.

An **event** is any measurable action taken by a user.

Examples of events:

```
page_view
view_item
add_to_cart
begin_checkout
purchase
click
scroll
```

Each event can include additional data called **parameters**.

Example purchase event:

```
event: purchase
value: 120
currency: USD
transaction_id: 12345
items: product data
```

These parameters allow analytics platforms to produce detailed reports.



## 7. Where Tracking Scripts Run

Most tracking scripts run in the **user's browser**.

This is called **client-side tracking**.

Tracking flow:

```
User browser loads webpage
↓
Tracking script executes
↓
User actions detected
↓
Data sent to analytics servers
```

This is the most common tracking method used today.



## 8. Client-Side vs Server-Side Tracking

There are two major tracking methods.

### Client-Side Tracking

This is the most common implementation.

Tracking happens directly in the user's browser.

Example flow:

```
User opens website
↓
Browser loads tracking script
↓
Script detects event
↓
Event sent to analytics server
```

Advantages:

* easy to implement
* flexible
* widely supported

Limitations:

* ad blockers may block scripts
* browser privacy restrictions
* cookie limitations

### Server-Side Tracking

Server-side tracking sends events from the **website server** instead of the browser.

Example flow:

```
User action occurs
↓
Website server records event
↓
Server sends event to analytics platform
```

Benefits:

* improved data reliability
* less affected by ad blockers
* better privacy control

Server-side tracking is becoming more common as browsers restrict client-side tracking.



## 9. What Happens When a User Visits a Website

Let’s walk through what happens when someone visits a Shopify store.

Example flow:

```
User opens product page
↓
Browser loads page HTML
↓
Tracking scripts load
↓
Google Tag Manager loads
↓
GTM fires GA4 tag
↓
Page_view event sent to Google Analytics
```

If the user interacts with the page:

```
User clicks Add to Cart
↓
Tracking event fires
↓
add_to_cart event sent to analytics platform
```

If the user purchases:

```
User completes checkout
↓
purchase event fires
↓
Revenue data sent to analytics platform
```



## 10. Why Tracking Accuracy Matters

Incorrect tracking can cause serious problems.

Examples of common tracking issues:

```
Duplicate purchase events
Missing add_to_cart events
Incorrect revenue values
Broken attribution
```

These problems can lead to:

* incorrect marketing decisions
* wasted ad spend
* inaccurate business reports

For example:

If purchases are counted twice, revenue reports become inflated.

This is why **tracking audits are extremely important**.



## 11. The Role of Tag Management Systems

As websites add more tracking tools, managing scripts becomes difficult.

Example stack:

```
Google Analytics
Google Ads
Meta Pixel
TikTok Pixel
Heatmap tools
A/B testing tools
Marketing automation
```

Instead of adding each script manually, companies use **tag management systems**.

A tag manager allows all tracking scripts to be managed from **one place**.

The most widely used tag manager is **Google Tag Manager**.

In the next article, we will explore **Google Analytics**, the most common analytics platform used for website measurement.



## Key Takeaways

Important concepts from this article:

* Website tracking collects user behavior data.
* Tracking is performed using **JavaScript tags**.
* Tags detect **events** and send data to analytics platforms.
* Most tracking occurs **in the browser (client-side)**.
* Accurate tracking is essential for reliable analytics.
* Tag managers simplify the management of tracking scripts.



## Next Article

### 📘 ARTICLE 2 — Introduction to Google Analytics

In the next article, we will cover:

* What Google Analytics is
* How Google Analytics collects data
* What GA4 Measurement IDs are
* How GA4 events work
* The difference between GA4 and earlier versions of Google Analytics

This will provide the foundation needed before we explore **Google Tag Manager and Shopify tracking implementations** later in the series.
