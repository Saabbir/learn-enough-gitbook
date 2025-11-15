# The Complete Guide to the Shopify Cart AJAX API

_Everything you need to build modern cart drawers, mini carts, quantity controls, and dynamic add-to-cart experiences._

If you're building a custom Shopify storefront experience — whether it’s a cart drawer, quantity selector, sticky add-to-cart bar, or a fully dynamic mini-cart — you will inevitably rely on the **Shopify Cart AJAX API**.

This API lets you interact with the cart using JavaScript, without page reloads and without needing an app or backend server.

In this guide, we’ll cover **only** the Cart API endpoints — nothing more, nothing less.\
Clear. Concise. Practical.

***

## **1. What Is the Shopify Cart AJAX API?**

The Shopify Cart AJAX API is a set of endpoints that let you:

* Add items to the cart
* Update quantities
* Remove items
* Clear the cart
* Retrieve the latest cart state
* Request re-rendered HTML sections

You call these endpoints from JavaScript inside a Shopify theme.

#### 💡 No authentication required.

#### 💡 Works on every Shopify storefront.

#### 💡 Designed for theme developers (not app devs).

***

## **2. The Cart API Endpoints**

Here are the only endpoints you need to know:

| Action                | Endpoint          | Method | Body Type |
| --------------------- | ----------------- | ------ | --------- |
| Add item              | `/cart/add.js`    | POST   | FormData  |
| Update multiple items | `/cart/update.js` | POST   | JSON      |
| Change single item    | `/cart/change.js` | POST   | JSON      |
| Clear cart            | `/cart/clear.js`  | POST   | JSON      |
| Fetch cart            | `/cart.js`        | GET    | —         |

Let’s break them down one by one.

***

## **3. Add to Cart — `/cart/add.js`**

This endpoint adds a variant to the cart.

#### **HTML Form Example**

```html
<form id="product-form">
  <input type="hidden" name="id" value="{{ product.selected_or_first_available_variant.id }}">
  <button>Add to Cart</button>
</form>
```

#### **JavaScript**

```js
const form = document.querySelector("#product-form");
const body = new FormData(form);

// Optional: Request updated section HTML
body.append("sections", "cart-drawer header-cart-icon");

fetch("/cart/add.js", {
  method: "POST",
  body
})
  .then(res => res.json())
  .then(data => {
    console.log("Item added:", data);
  })
  .catch(console.error);
```

#### ⚠ Why use **FormData**, not JSON?

Because Shopify expects the same payload as a product form submission.\
JSON will not work for adding products.

***

## **4. Update the Cart — `/cart/update.js`**

This endpoint updates multiple line items at once.

#### **Example: Update quantity**

```js
fetch("/cart/update.js", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    updates: {
      [lineItemKey]: 2,   // Set quantity to 2
    },
    sections: "cart-drawer"
  })
})
  .then(res => res.json())
  .then(data => console.log("Cart updated:", data));
```

#### **Remove an item**

Just set quantity to `0`:

```js
updates: {
  [lineItemKey]: 0
}
```

#### 📌 What is a **line item key**?

Every cart item has a unique key (not the variant ID).\
It encodes:

* variant ID
* selling plan
* custom properties

You can view it via:

```js
fetch('/cart.js').then(r => r.json()).then(cart => console.log(cart.items));
```

***

## **5. Update Single Item — `/cart/change.js`**

This is a more targeted way to update ONE line item.

#### **Example**

```js
fetch("/cart/change.js", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    id: lineItemKey,
    quantity: 3,
    sections: "cart-drawer"
  })
})
  .then(res => res.json())
  .then(data => console.log("Item changed:", data));
```

Useful for:

* Increment / decrement buttons
* Cart drawers
* Quick carts

***

## **6. Clear the Cart — `/cart/clear.js`**

This wipes the cart completely.

```js
fetch("/cart/clear.js", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    sections: "cart-drawer"
  })
})
  .then(res => res.json())
  .then(data => console.log("Cart cleared:", data));
```

Great for:

* Post-checkout redirection
* Resetting cart for special flows
* “Start over” type UX

***

## **7. Fetch Cart State — `/cart.js`**

This returns the _entire cart object_.

```js
fetch("/cart.js")
  .then(res => res.json())
  .then(cart => console.log(cart));
```

The returned data includes:

* Items
* Quantities
* Totals
* Attributes
* Cart-level discount codes
* Cart token

Useful for:

* Cart counters
* Custom cart rendering
* Side cart UI

***

## **8. Section Rendering in the Cart API**

All cart endpoints support a powerful feature:

> **Ask Shopify to re-render any section and return the HTML.**

#### Example

```json
{
  "sections": "cart-drawer cart-icon"
}
```

Shopify responds with:

```json
{
  "sections": {
    "cart-drawer": "<div>Updated drawer markup...</div>",
    "cart-icon": "<span>3</span>"
  }
}
```

#### Why this matters:

* You don’t need to manually query DOM and rebuild HTML
* You don’t need JS templating
* You use the theme’s own Liquid templates
* The server returns perfectly rendered, up-to-date HTML

This is how all modern mini-carts work.

***

## **9. Best Practices for Using the Shopify Cart API**

#### ✅ Use FormData for add-to-cart

JSON won’t work for product forms.

#### ✅ Always request updated sections

This keeps your UI in sync with Liquid.

#### ✅ Don’t spam the API

Debounce quantity changes.

#### ✅ Handle errors gracefully

Stock issues and selling plan issues can occur.

#### ✅ Store line item keys

You’ll need them frequently.

#### ✅ Avoid custom cart-token hacks

Shopify manages cart sessions for you.

***

## **10. Common Mistakes to Avoid**

#### ❌ Using variant ID instead of line item key when updating

Updating only works with line item keys.

#### ❌ Forgetting to set headers for JSON requests

Use:

```js
headers: { "Content-Type": "application/json" }
```

#### ❌ Expecting `/cart.js` to return sections

Only endpoints that accept JSON or FormData can return sections.

#### ❌ Using JSON instead of FormData for add-to-cart

`/cart/add.js` will fail.

#### ❌ Forgetting to update multiple UI areas

Use multiple section IDs if needed.

***

## **Conclusion — You Are Now Ready to Build Modern Shopify Cart Experiences**

You now understand:

* The cart endpoints
* How to add to cart
* How to update or remove items
* How to clear the cart
* How to fetch cart data
* How section rendering works
* Best practices for real themes

With the Shopify Cart AJAX API, you can build:

* Cart drawers
* Header cart counters
* Sticky add-to-cart features
* Mini carts
* Interactive product pages
* Custom checkout flows

No page reloads. No apps. No backend code.
