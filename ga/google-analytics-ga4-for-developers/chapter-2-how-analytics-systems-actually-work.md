# 📘 CHAPTER 2 — How Analytics Systems Actually Work

## Why This Chapter Matters

Most tutorials teach **how to click around GA reports**.

Very few explain **how analytics systems actually collect and process data**.

But if you are a **developer**, this is the most important thing to understand.

Once you understand the pipeline, you will know how to:

* debug tracking issues
* understand missing data
* validate Shopify vs GA numbers
* diagnose A/B test discrepancies

The key idea:

> Analytics is a **data pipeline**.



## The Analytics Data Pipeline

Every analytics system follows the same architecture.

```
User Action
     ↓
Tracking Script
     ↓
Event Request (Network)
     ↓
Analytics Collection Server
     ↓
Processing Pipeline
     ↓
Data Storage
     ↓
Reports
```

Let’s go through each step.



## Step 1 — User Action Happens

Everything begins with a user interaction.

Examples:

| Action            | Event         |
| ----------------- | ------------- |
| visit page        | page\_view    |
| view product      | view\_item    |
| click add to cart | add\_to\_cart |
| purchase product  | purchase      |

The website must detect these actions.



## Step 2 — Tracking Script Detects the Action

Your website contains a **tracking script**.

Example:

```
Google tag (gtag.js)
```

or

```
Google Tag Manager
```

This script listens for events.

Example:

```javascript
gtag('event', 'add_to_cart', {
  value: 60,
  currency: 'GBP'
});
```

The script prepares the event data.



## Step 3 — The Browser Sends a Network Request

Once an event is triggered, the browser sends a request to Google Analytics servers.

Example request:

```
https://www.google-analytics.com/g/collect
```

This request contains data such as:

```
event_name=add_to_cart
value=60
currency=GBP
page=/products/jacket
device=mobile
```

This is the **actual moment data leaves the user’s browser**.

Developers can inspect this in:

```
Chrome DevTools → Network → collect
```



## Step 4 — Analytics Collection Servers Receive the Event

Google receives the event request.

At this point the event is:

```
Raw event data
```

Example:

```
event_name: add_to_cart
user_id: abc123
timestamp: 10:03
```

But the event is **not yet visible in reports**.



## Step 5 — Data Processing Pipeline

Google processes events before making them available.

This includes:

| Processing Step | What Happens                |
| --------------- | --------------------------- |
| validation      | checks event format         |
| attribution     | assigns traffic source      |
| sessionization  | groups events into sessions |
| deduplication   | removes duplicate events    |
| aggregation     | prepares reporting tables   |

This is why analytics data is **not immediately available**.



## Step 6 — Data Storage

After processing, the event is stored in Google's analytics database.

Conceptually the database looks like this:

| event\_name   | page             | device | country | timestamp |
| ------------- | ---------------- | ------ | ------- | --------- |
| page\_view    | /                | mobile | UK      | 10:00     |
| view\_item    | /products/jacket | mobile | UK      | 10:01     |
| add\_to\_cart | /products/jacket | mobile | UK      | 10:02     |
| purchase      | checkout         | mobile | UK      | 10:05     |

From this dataset GA builds reports.



## Step 7 — Reports Query the Data

When you open a report like:

```
Monetization → Ecommerce purchases
```

GA runs a query like:

```
COUNT events WHERE event_name = purchase
```

or

```
SUM revenue GROUP BY country
```

Reports are simply **visualized queries on stored events**.



## Real-Time vs Processed Data

GA shows data at different speeds.

| Data Type            | Speed             |
| -------------------- | ----------------- |
| DebugView            | immediate         |
| Realtime report      | seconds           |
| Standard reports     | hours             |
| Final processed data | up to 24–48 hours |

This delay exists because of the **processing pipeline**.



## Where Data Gets Lost

This is something most tutorials **never explain**.

Analytics systems **never capture 100% of events**.

Data loss happens at several points.

### 1 — Ad Blockers

Ad blockers often block analytics requests.

Example blocked request:

```
google-analytics.com/g/collect
```

Result:

```
Event never reaches GA
```

### 2 — Browser Privacy Restrictions

Modern browsers limit tracking.

Examples:

| Browser | Restriction                  |
| ------- | ---------------------------- |
| Safari  | ITP tracking limits          |
| Firefox | enhanced tracking protection |
| Brave   | aggressive blocking          |

### 3 — Page Closed Too Quickly

If a user leaves before the event request finishes, the event may never reach GA.

Example:

```
purchase event fired
user closes tab
request cancelled
```

### 4 — JavaScript Errors

If the tracking script breaks, events stop sending.

Example:

```
TypeError: undefined is not a function
```

Tracking fails silently.

### 5 — Network Failures

Poor connections can cancel requests.



## Why Analytics Numbers Never Match

Because of these factors, analytics numbers always differ across systems.

Example:

| System  | Purchases |
| ------- | --------- |
| Shopify | 120       |
| GA      | 110       |
| Convert | 105       |

This is normal.

Each system collects data differently.



## The Analytics Reliability Rule

Most analytics engineers follow this rule:

> **Within 5–15% difference is normal.**

Example:

| System  | Purchases |
| ------- | --------- |
| Shopify | 120       |
| GA      | 113       |

This difference is expected.



## Developer Debugging Tip

Whenever something looks wrong, always check the **network request first**.

Open:

```
Chrome DevTools
Network
Filter: collect
```

Look for requests like:

```
https://www.google-analytics.com/g/collect
```

Verify:

* event name
* parameters
* values

If the request exists, the event **left the browser**.



## Key Takeaways

Analytics systems are **data pipelines**.

Data flows through these steps:

```
User action
→ Tracking script
→ Network request
→ Analytics servers
→ Data processing
→ Reports
```

Important lessons:

* analytics data is **never perfectly accurate**
* reports are **queries on event datasets**
* data loss can happen at many stages

Understanding this pipeline makes debugging much easier.



## Next Chapter

### Chapter 3 — Why Analytics Numbers Never Match

This is one of the **most misunderstood topics in analytics**.

We will explain:

* why GA, Shopify, and A/B testing tools show different numbers
* attribution differences
* session differences
* user tracking limitations
* real-world discrepancy examples

This knowledge will save you **hundreds of hours of debugging confusion**.
