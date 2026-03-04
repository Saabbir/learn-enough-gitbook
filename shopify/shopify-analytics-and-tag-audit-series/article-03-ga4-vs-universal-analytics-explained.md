# 📘 ARTICLE 03 — GA4 vs Universal Analytics Explained

In the previous article, we introduced **Google Analytics** and learned how GA4 tracks user interactions through events.

However, many websites, including Shopify stores, still contain remnants of an older analytics system called **Universal Analytics (UA)**.

Understanding the differences between **GA4 and Universal Analytics** is important because during audits you may encounter:

* old UA scripts
* hybrid GA4 + UA implementations
* legacy e-commerce tracking
* migration mistakes

This article explains **how Google Analytics evolved and why GA4 replaced Universal Analytics**.



## 1. The Evolution of Google Analytics

Google Analytics has gone through several generations.

#### 1. Classic Google Analytics (2005)

Early version of Google Analytics.

Script example:

```
ga.js
```

Tracking focused on:

```
pageviews
sessions
referrers
```

#### 2. Universal Analytics (2012)

Universal Analytics introduced:

* improved tracking architecture
* custom dimensions
* e-commerce tracking

Tracking ID format:

```
UA-XXXXXXXX-X
```

Example:

```
UA-12345678-1
```

This became the **standard analytics system for nearly a decade**.

#### 3. Google Analytics 4 (2020)

Google introduced GA4 to adapt to:

* mobile apps
* privacy regulations
* cross-device behavior

Measurement ID format:

```
G-XXXXXXXXXX
```

Example:

```
G-9V5CXZEBCN
```

GA4 uses a completely different **data model**.



## 2. Why Universal Analytics Was Replaced

Universal Analytics had several limitations.

#### 1. Session-based architecture

UA focused heavily on sessions.

Example flow:

```
User visits website
↓
Session starts
↓
User performs actions
↓
Session ends
```

However, modern user behavior is more complex:

* users switch devices
* users return multiple times
* users interact across apps and websites

UA struggled to track these journeys accurately.

#### 2. Weak cross-device tracking

Example user journey:

```
User discovers product on mobile
↓
Later visits website on laptop
↓
Completes purchase
```

Universal Analytics often counted these as **two different users**.

#### 3. Limited event tracking

In UA, event tracking required manual configuration.

Example event structure:

```
category
action
label
value
```

Example:

```
Category: Ecommerce
Action: Add to Cart
Label: Product Name
```

This structure was rigid and difficult to scale.

#### 4. Privacy & cookie restrictions

Modern browsers introduced privacy protections such as:

* cookie limitations
* tracking prevention
* consent requirements

UA was not designed for these restrictions.



## 3. The GA4 Data Model

GA4 introduced a new **event-based tracking model**.

Instead of sessions and pageviews being the core unit of measurement, **everything is an event**.

Example:

```
page_view
scroll
click
view_item
add_to_cart
purchase
```

Each event can include **parameters**.

Example purchase event:

```
event: purchase
transaction_id: 12345
value: 150
currency: USD
items: product data
```

This model provides greater flexibility.



## 4. Event-Based Tracking Explained

In GA4, every interaction is stored as an event.

Example user journey:

```
page_view
view_item
add_to_cart
begin_checkout
purchase
```

Each step is recorded as a separate event.

This allows GA4 to build:

* funnels
* user journeys
* e-commerce reports



## 5. Key Differences Between UA and GA4

| Feature                   | Universal Analytics   | GA4                 |
| ------------------------- | --------------------- | ------------------- |
| Tracking model            | Session based         | Event based         |
| Tracking ID               | UA-XXXXXXXX-X         | G-XXXXXXXXXX        |
| Event structure           | category/action/label | flexible parameters |
| Cross-device tracking     | limited               | improved            |
| Machine learning insights | minimal               | advanced            |
| App tracking              | limited               | native support      |



## 6. Ecommerce Tracking Differences

Universal Analytics e-commerce tracking required special configuration.

Example events:

```
product impressions
product clicks
add to cart
checkout
purchase
```

GA4 uses **recommended e-commerce events**.

Example GA4 events:

```
view_item
add_to_cart
begin_checkout
purchase
```

Each event includes **structured parameters**.

Example:

```
items
price
quantity
currency
transaction_id
```



## 7. How to Recognize UA vs GA4 on a Website

During audits, you should be able to identify which system is installed.

### Universal Analytics Script

Example:

```
analytics.js
```

Tracking code:

```
UA-XXXXXXXX-X
```

Example script:

```
ga('create', 'UA-12345678-1', 'auto');
```

### GA4 Script

Example:

```
gtag.js
```

Tracking ID:

```
G-XXXXXXXXXX
```

Example script:

```
gtag('config', 'G-XXXXXXXXXX');
```



## 8. Why UA Still Appears During Audits

Although UA was deprecated in **July 2023**, many websites still contain:

* leftover UA scripts
* old Google Tag Manager tags
* legacy analytics implementations

Reasons include:

```
website migrations
old theme files
forgotten scripts
third-party plugins
```

During audits, you may find the following:

```
UA tracking still installed but inactive
GA4 + UA running simultaneously
duplicate tracking scripts
```



## 9. Common Migration Mistakes

When websites migrated from UA to GA4, several mistakes occurred.

### Running UA and GA4 simultaneously

This caused:

```
duplicate analytics events
confusing reports
data inconsistencies
```

### Incorrect e-commerce event mapping

Example:

```
add_to_cart event mapped incorrectly
```

Result:

```
missing ecommerce data
```

### Missing purchase events

Some migrations failed to correctly implement the **purchase event**.

Result:

```
revenue not recorded
conversion rate incorrect
```



## 10. What Auditors Should Check

When auditing analytics setups, check for:

```
UA scripts still present
GA4 scripts installed correctly
duplicate tracking systems
incorrect event mappings
```

You should also verify:

```
purchase events firing correctly
transaction IDs present
revenue values correct
```



## Key Takeaways

Important concepts from this article:

* Universal Analytics used **session-based tracking**.
* GA4 uses **event-based tracking**.
* UA tracking IDs start with **UA-**.
* GA4 measurement IDs start with **G-**.
* UA was deprecated in **2023**.
* Many websites still contain legacy UA scripts.

Understanding both systems helps identify **incorrect implementations during analytics audits**.



## Next Article

### 📘 ARTICLE 04 — Understanding Google Tag Manager

In the next article, we will explore:

* What Google Tag Manager is
* Why tag managers exist
* GTM container structure
* Tags, triggers, and variables
* How GTM loads analytics scripts
* Why GTM is essential for managing tracking on Shopify sites

This article will introduce one of the **most important tools for analytics auditing and implementation**.
