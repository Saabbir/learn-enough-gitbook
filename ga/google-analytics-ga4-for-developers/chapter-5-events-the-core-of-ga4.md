# 📘 CHAPTER 5 — Events (The Core of GA4)

## Why This Chapter Matters

If **Google Analytics had a single core concept**, it would be **events**.

In GA4:

> **Everything is an event.**

This includes:

* page views
* clicks
* scrolls
* purchases
* video plays

Understanding events properly will allow you to:

* debug tracking problems
* validate e-commerce data
* design clean analytics implementations
* understand reports correctly

For developers, events are **the heart of analytics**.



## The Core Idea

An **event** represents a **user interaction**.

Example user journey on a Shopify store:

```
page_view
view_item
add_to_cart
begin_checkout
purchase
```

Each step is recorded as an event.

GA stores these events and builds reports from them.



## What an Event Looks Like

An event is simply a **data record** describing an action.

Example purchase event:

```
event_name: purchase

parameters:
value: 120
currency: GBP
transaction_id: 48392
```

This event tells GA:

* what happened → purchase
* how much revenue → £120
* which transaction → order ID



## Event Structure

All GA events follow this structure:

```
event_name
   ↓
parameters
```

Example:

```
add_to_cart
 ├ currency: GBP
 ├ value: 60
 └ items: jacket
```

The **event name describes the action**, while **parameters provide details**.



## Types of GA Events

GA4 has **four categories of events**.

Understanding these categories is important.

| Event Type                  | Description                        |
| --------------------------- | ---------------------------------- |
| Automatic events            | collected automatically            |
| Enhanced measurement events | automatically tracked interactions |
| Recommended events          | predefined by Google               |
| Custom events               | developer-defined                  |



## 1 — Automatic Events

These events are collected automatically by GA.

Examples:

| Event          | Meaning             |
| -------------- | ------------------- |
| first\_visit   | user's first visit  |
| session\_start | new session started |
| page\_view     | page loaded         |

Example:

```
event_name: page_view
page_location: /collections/shirts
```

Developers usually **do not implement these manually**.



## 2 — Enhanced Measurement Events

These events are enabled in GA settings.

They automatically track common interactions.

Examples:

| Event           | Meaning                    |
| --------------- | -------------------------- |
| scroll          | user scrolls 90% down page |
| outbound\_click | click to external site     |
| file\_download  | file download              |
| video\_start    | YouTube video started      |

These require **minimal setup**.



## 3 — Recommended Events

These are events defined by Google for common use cases.

Examples:

| Event         | Meaning            |
| ------------- | ------------------ |
| login         | user logs in       |
| sign\_up      | account created    |
| add\_to\_cart | item added to cart |
| purchase      | order completed    |

Using recommended events ensures:

* compatibility with GA reports
* better analytics insights
* standardized tracking



## 4 — Custom Events

Custom events are defined by developers.

Example:

```
newsletter_signup
product_filter_used
wishlist_added
```

Example implementation:

```javascript
gtag('event', 'newsletter_signup', {
  location: 'footer'
});
```

These events allow tracking **business-specific actions**.



## Event Naming Best Practices

Event names should be:

* clear
* consistent
* predictable

Good examples:

```
add_to_cart
begin_checkout
purchase
```

Bad examples:

```
clickedAdd
cartBtn
orderDone123
```

Recommended rules:

| Rule            | Example            |
| --------------- | ------------------ |
| use lowercase   | add\_to\_cart      |
| use underscores | begin\_checkout    |
| be descriptive  | newsletter\_signup |



## Ecommerce Events (Most Important for Shopify)

Ecommerce stores rely heavily on these events.

Key e-commerce events:

```
view_item
add_to_cart
view_cart
begin_checkout
purchase
```

Example purchase event:

```
event_name: purchase

parameters:
value: 120
currency: GBP
transaction_id: 48291
```

This powers GA ecommerce reports.



## Event Sequence Example

Typical Shopify purchase journey:

```
page_view
view_item
add_to_cart
view_cart
begin_checkout
purchase
```

GA can analyze:

* drop-off points
* conversion rate
* product performance



## Event vs Page View

Many developers confuse these.

| Concept    | Meaning          |
| ---------- | ---------------- |
| page\_view | page loaded      |
| event      | user interaction |

Example:

User visits product page:

```
page_view
```

User clicks add to cart:

```
add_to_cart
```



## How Events Are Sent to GA

Events are sent using **tracking scripts**.

Example using gtag:

```javascript
gtag('event', 'add_to_cart', {
  currency: 'GBP',
  value: 60
});
```

This creates a network request to GA servers.

Developers can inspect it using:

```
Chrome DevTools
→ Network
→ collect request
```



## Common Event Implementation Mistakes

These are extremely common.



### 1 — Missing parameters

Example mistake:

```
purchase
```

Without:

```
value
currency
```

Result:

Revenue reports break.



### 2 — Duplicate event triggers

Example problem:

```
purchase fires twice
```

Cause:

* multiple GTM triggers
* page reload on thank-you page



### 3 — Wrong event names

Example mistake:

```
order_complete
```

Instead of:

```
purchase
```

GA ecommerce reports rely on **standard names**.



## Debugging Events

When debugging events, always follow this process.

#### Step 1

Check if the event fires in the browser.

Use:

```
Chrome DevTools
→ Network
→ collect
```

#### Step 2

Verify parameters.

Example:

```
event_name: purchase
value: 120
currency: GBP
```

#### Step 3

Check GA DebugView.

```
GA → Admin → DebugView
```

Confirm the event appears.



## Event Flow Visualization

```
User action
     ↓
Tracking script
     ↓
Event sent to GA
     ↓
Stored in analytics database
     ↓
Reports generated
```

Events are the **foundation of all analytics data**.



## Key Takeaways

Important lessons:

1. Everything in GA4 is an **event**.
2. Events describe **user interactions**.
3. Events contain **parameters with details**.
4. Recommended events should be used when possible.
5. Clean event naming improves analytics quality.



## Next Chapter

### Chapter 6 — Event Parameters

Events alone are not enough.

Parameters provide **the real data that powers reports**.

In the next chapter, we will explain:

* event parameters in depth
* ecommerce parameters
* how parameters become dimensions and metrics
* common implementation mistakes
* debugging parameter issues

Understanding parameters will unlock **advanced analytics reporting**.
