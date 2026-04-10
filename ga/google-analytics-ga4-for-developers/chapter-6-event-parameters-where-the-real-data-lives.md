# CHAPTER 6 — Event Parameters (Where the Real Data Lives)

## Why This Chapter Matters

Events tell you **what happened**.

Parameters tell you **everything about what happened**.

Without parameters, your analytics become **almost useless**.

Example:

```
event_name: purchase
```

This tells you a purchase happened — but:

* How much revenue? ❌
* Which product? ❌
* Which order? ❌

Now compare:

```
event_name: purchase
value: 120
currency: GBP
transaction_id: 48392
items: [jacket]
```

Now you have **real business data**.



## The Core Idea

> **Parameters = additional data attached to an event**

Structure:

```
event_name
   ↓
parameters (key-value pairs)
```

Example:

```
add_to_cart
 ├ value: 60
 ├ currency: GBP
 └ item_name: Jacket
```



## Types of Parameters

There are **three main types** of parameters in GA.

| Type                    | Description           |
| ----------------------- | --------------------- |
| Automatically collected | added by GA           |
| Recommended parameters  | defined by Google     |
| Custom parameters       | defined by developers |



## 1 — Automatically Collected Parameters

GA adds some parameters automatically.

Examples:

| Parameter          | Meaning            |
| ------------------ | ------------------ |
| page\_location     | current URL        |
| page\_title        | page title         |
| language           | browser language   |
| screen\_resolution | device screen size |

Example:

```
event_name: page_view
page_location: /collections/shirts
device: mobile
```



## 2 — Recommended Parameters

These are tied to **recommended events**.

Example for `purchase`:

| Parameter       | Required? |
| --------------- | --------- |
| value           | ✅         |
| currency        | ✅         |
| transaction\_id | ✅         |
| items           | ✅         |

Example:

```
purchase
 ├ value: 120
 ├ currency: GBP
 ├ transaction_id: 12345
 └ items: [...]
```

Using recommended parameters ensures:

* GA e-commerce reports work correctly
* data is structured properly



## 3 — Custom Parameters

These are defined by developers for business-specific needs.

Examples:

```
product_color: red
membership_type: premium
experiment_variant: B
```

Example implementation:

```javascript
gtag('event', 'add_to_cart', {
  value: 60,
  currency: 'GBP',
  product_color: 'red'
});
```



## Ecommerce Parameters (Most Important)

For Shopify/e-commerce, parameters are critical.



### The `items` Array

This is the most important parameter.

Example:

```
items:
  [
    {
      item_id: "SKU123",
      item_name: "Wool Jacket",
      price: 120,
      quantity: 1
    }
  ]
```

This enables:

* product performance reports
* revenue per product
* cart analysis



### Key Ecommerce Parameters

| Parameter       | Purpose         |
| --------------- | --------------- |
| value           | total revenue   |
| currency        | currency code   |
| transaction\_id | unique order ID |
| items           | product details |
| tax             | tax amount      |
| shipping        | shipping cost   |



## How Parameters Become Reports

This is a **critical concept**.

> Parameters are converted into **dimensions and metrics**.

### Example

Raw event:

```
purchase
value: 120
currency: GBP
country: UK
```

Becomes a report:

| Country (dimension) | Revenue (metric) |
| ------------------- | ---------------- |
| UK                  | 120              |

### Mapping Example

| Parameter | Becomes   |
| --------- | --------- |
| country   | dimension |
| device    | dimension |
| value     | metric    |
| quantity  | metric    |



## Custom Dimensions (Important for Developers)

Custom parameters are **not automatically visible in GA reports**.

You must register them.

Example parameter:

```
experiment_variant: B
```

To use it in reports:

```
GA Admin → Custom Definitions → Create Dimension
```

Otherwise:

❌ Data exists but not visible\
❌ Reports cannot use it



## Real Developer Example (A/B Testing)

Tracking experiment variant:

```javascript
gtag('event', 'purchase', {
  value: 120,
  currency: 'GBP',
  experiment_variant: 'B'
});
```

Then create a custom dimension:

```
experiment_variant
```

Now you can build a report:

| Variant | Revenue |
| ------- | ------- |
| A       | £5000   |
| B       | £6200   |



## Common Parameter Mistakes

These are extremely common.



### 1 — Missing Required Parameters

Example:

```
purchase
```

Without:

```
value
currency
```

Result:

* purchases counted ✅
* revenue missing ❌



### 2 — Incorrect Data Types

Example mistake:

```
value: "120"   (string ❌)
```

Correct:

```
value: 120     (number ✅)
```



### 3 — Missing transaction\_id

Without transaction ID:

* GA cannot deduplicate purchases
* duplicate revenue may occur



### 4 — Broken items array

Example mistake:

```
items: "jacket"
```

Correct:

```
items: [
  { item_name: "Jacket" }
]
```



## Debugging Parameters

#### Step 1 — Check Network Request

Open:

```
Chrome DevTools → Network → collect
```

Inspect payload:

```
value=120
currency=GBP
```

#### Step 2 — Check GA DebugView

Verify:

* event received
* parameters present

#### Step 3 — Check Reports

If a parameter is missing in the report:

* check custom dimension setup
* check naming consistency



## Parameter Flow

```
User action
     ↓
Event triggered
     ↓
Parameters attached
     ↓
Sent to GA
     ↓
Processed into dimensions/metrics
     ↓
Visible in reports
```



## Developer Mental Model

Think of parameters as:

> **The payload of your analytics system**

Events = structure\
Parameters = actual data



## Key Takeaways

1. Parameters give meaning to events
2. Without parameters, reports are useless
3. E-commerce tracking depends heavily on parameters
4. Custom parameters require custom dimensions
5. Always validate parameters during debugging



## Next Chapter

### Chapter 7 — Dimensions (How Data Gets Grouped)

Now that you understand parameters, we’ll go deeper into:

* what dimensions really are
* how GA groups data
* primary vs secondary dimensions
* custom dimensions
* common mistakes developers make

This will help you understand **how reports are actually built**.
