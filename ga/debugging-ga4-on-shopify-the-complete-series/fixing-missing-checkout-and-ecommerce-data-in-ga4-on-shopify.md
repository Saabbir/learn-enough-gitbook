---
icon: '5'
---

# Fixing Missing Checkout and Ecommerce Data in GA4 on Shopify

{% hint style="info" %}
_Part 5 of 6 in the Debugging GA4 on Shopify series._
{% endhint %}

GA4 can load perfectly and still miss the data that matters most: checkouts, purchases and revenue. This part covers the four most common causes on Shopify: nothing tracking checkout, a consent banner blocking tags, sandbox URLs in place of real page paths, and ecommerce parameters that are missing or wrong.

> **New to this series?** GA4 on Shopify can run in two places: on the page itself (theme code) or inside Shopify's pixel sandbox (custom pixels and app pixels). It is also either _explicit_ (code your team wrote and can edit) or _implicit_ (managed by Shopify, Google or an app). Part 1 shows how to tell which setup you have.



## Use case 3: Purchase or checkout events are missing

If storefront events work but `begin_checkout` or `purchase` don't, nothing is tracking the checkout, because theme code never runs there. Only a web pixel (Google & YouTube app, a custom pixel or a tracking app) can.

**Symptoms:** Monetization → Ecommerce purchases shows 0 or far fewer orders than Shopify; `add_to_cart` exists but the funnel stops at checkout; purchases dropped sharply right after the Thank you page upgrade (deadline August 28, 2025 for Plus, August 26, 2026 for other plans).

**Checks:**

1. **Is anything covering checkout?** If your only GA code is in `theme.liquid` or GTM-in-theme, checkout is untracked.
2. **Leftover legacy scripts?** Old tags in `checkout.liquid` or Order status → Additional scripts no longer fire. Move them to a pixel.
3. **Right Shopify event names?** Pixels subscribe to Shopify's names, not GA4's. Subscribing to `purchase` does nothing.
4. **Did you test with a real completed order?** Reloading an old thank-you URL won't fire `checkout_completed`; it fires once per checkout.
5. **Pixel Helper on the thank-you page:** is `checkout_completed` green? Then look in Network for `collect` with `en=purchase`.

**If you use the Google & YouTube app:** no browser `purchase` hit is expected. Since July 2026 the app's purchase goes from Shopify's servers to GA4, and Google deduplicates it against browser purchases by transaction ID ([Analyzify](https://analyzify.com/hub/google-shopify-server-side-integration)). Verify it in GA4 Realtime a few minutes after the test order instead.

| Shopify event (what you subscribe to) | GA4 event (what you send) |
| ------------------------------------- | ------------------------- |
| `page_viewed`                         | `page_view`               |
| `product_viewed`                      | `view_item`               |
| `collection_viewed`                   | `view_item_list`          |
| `search_submitted`                    | `search`                  |
| `product_added_to_cart`               | `add_to_cart`             |
| `product_removed_from_cart`           | `remove_from_cart`        |
| `cart_viewed`                         | `view_cart`               |
| `checkout_started`                    | `begin_checkout`          |
| `checkout_shipping_info_submitted`    | `add_shipping_info`       |
| `payment_info_submitted`              | `add_payment_info`        |
| `checkout_completed`                  | `purchase`                |

Shopify also emits `checkout_contact_info_submitted` and `checkout_address_info_submitted`, which have no standard GA4 equivalent; send them as custom events if you need them ([Shopify.dev](https://shopify.dev/docs/api/web-pixels-api/standard-events)). `checkout_completed` fires once per checkout, normally on the Thank you page, but on the first upsell page if you run post-purchase offers. If that page fails to load, it doesn't fire at all ([Shopify.dev](https://shopify.dev/docs/api/web-pixels-api/standard-events/checkout_completed)).

**How to place a test order without paying:** Settings → Payments → Shopify Payments → enable **Test mode**, then pay with card `4242 4242 4242 4242`, any future expiry and any CVC. On stores without Shopify Payments, activate the (for testing) Bogus Gateway. Cancel and archive test orders afterwards.

**Example: purchase in a custom pixel**

```javascript
analytics.subscribe('checkout_completed', (event) => {
  const c = event.data.checkout;
  gtag('event', 'purchase', {
    transaction_id: c.order?.id || c.token,   // must be unique per order
    value: c.totalPrice?.amount,               // a number, e.g. 49.9
    currency: c.currencyCode,                  // e.g. "USD"
    tax: c.totalTax?.amount,
    shipping: c.shippingLine?.price?.amount,
    items: c.lineItems.map((li, i) => ({
      item_id: li.variant?.sku || li.variant?.id,
      item_name: li.title,
      item_variant: li.variant?.title,
      price: li.variant?.price?.amount,
      quantity: li.quantity,
      index: i
    }))
  });
});
```

**Worked example:** a store moved to the new Thank you page and GA4 purchases fell to zero overnight while Shopify orders stayed flat. The purchase tag lived in Additional scripts, which stopped running. Adding the custom pixel above (or connecting GA4 in the Google & YouTube app) restored them.



## Use case 5: The consent banner is blocking tracking

Shopify web pixels only run once the visitor has granted the permissions each pixel asks for, and by default new pixels require both Marketing and Analytics ([Lionel Z.](https://lionelz.com/en/blog/shopify-ga4-meta-capi-sin-app/)). A banner that only asks for analytics consent can silently switch off a pixel.

**Symptoms:** traffic dropped right after installing a cookie banner; EU/UK visitors are missing but US ones aren't; Pixel Helper shows "Pixel is awaiting consent"; `collect` hits carry `gcs=G100`.

**Checks:**

1. Settings → Customer privacy: which regions show a banner, and is it Shopify's or a third-party app's?
2. Settings → Customer events → open each pixel → **Customer privacy** permissions: which consent categories does it require before it runs?
3. Does your banner app write consent into Shopify's Customer Privacy API? If not, pixels never see a "yes".
4. In the console, check what Shopify thinks the visitor agreed to:

```javascript
window.Shopify?.customerPrivacy?.currentVisitorConsent();
// { analytics: "yes", marketing: "no", preferences: "", sale_of_data: "" }
```

5. In Network, read the `gcs` parameter on `collect` hits (Google Consent Mode):

| `gcs` value | ad\_storage | analytics\_storage |
| ----------- | ----------- | ------------------ |
| `G111`      | granted     | granted            |
| `G101`      | denied      | granted            |
| `G110`      | granted     | denied             |
| `G100`      | denied      | denied             |

**Worked example:** a store's custom GA4 pixel was left on the default (Marketing + Analytics). Their banner offered "Accept analytics" and "Accept all". Everyone who chose analytics-only was never tracked. Changing the pixel's permission to Analytics only fixed it.

**Consent Mode v2 tip for GTM setups:** the consent default (`gtag('consent', 'default', {...})`) must run before any Google tag. If the banner loads after GTM, early hits go out without consent state, and later updates may not apply to them. Test from a VPN in an EU country, and compare behavior before and after clicking Accept.



## Use case 6: Page paths full of sandbox junk

When gtag runs inside a custom pixel, it reads the iframe's own address, so GA4 records paths like `/wpm@960565ca…/custom/web-pixel-25755807@1/sandbox/modern/cart` instead of `/cart` ([Shopify Community](https://community.shopify.com/t/shopify-pixel-customer-events/324726/7)).

**Symptoms:** Pages and screens report full of `/wpm@` and `/sandbox/modern/` paths; landing pages all look the same; `dl=` in `collect` hits shows the sandbox URL.

**Fix:** turn off the automatic page view and pass the real URL from Shopify's event context on every event.

```javascript
gtag('config', MEASUREMENT_ID, { send_page_view: false });

analytics.subscribe('page_viewed', (event) => {
  gtag('event', 'page_view', {
    page_location: event.context.document.location.href,  // real URL
    page_referrer: event.context.document.referrer,
    page_title: event.context.document.title
  });
});
```

For GTM loaded inside a custom pixel, push `event.context.document.location.href` into the dataLayer and map it to the `page_location` field of the Google tag in GTM.

**Old data:** GA4 can't rewrite history. For reports covering the bad period, strip the prefix in Looker Studio with `REGEXP_REPLACE(Page path, "^/wpm@[^/]+/custom/web-pixel-[^/]+/sandbox/modern", "")`.



## Use case 7: Ecommerce values are missing or wrong

If purchases are counted but revenue, items or currency look wrong, the event fires but its parameters are malformed. GA4 event and parameter names are case-sensitive, and `value` needs a matching `currency` to count as revenue.

**How to inspect:** DebugView → click the event → Parameters and Items tabs. Or in Network, open the `collect` hit: `en=purchase`, `epn.value=49.9`, `cu=USD`, and one `pr1=`, `pr2=`… string per item (e.g. `pr1=idTSHIRT-BLK-M~nmClassic%20Tee~pr49.9~qt1`).

| Symptom                                                | Likely cause                                                 | Fix                                                                             |
| ------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| Purchases counted, revenue = 0                         | `value` missing, or sent as a string like `"$49.90"`         | Send a plain number: `value: 49.9`                                              |
| Revenue present but wrong currency, or not counted     | `currency` missing                                           | Always send `currency` (the ISO code, e.g. `"EUR"`)                             |
| Revenue 100x too high (4990 instead of 49.90)          | Liquid outputs prices in cents (`{{ product.price }}`)       | Use `{{ product.price \| divided_by: 100.0 }}`, or the pixel's `amount` fields  |
| Item name `(not set)` in item reports                  | `items` array missing, or items lack `item_id` / `item_name` | Send each item with at least `item_id` or `item_name`, `price`, `quantity`      |
| Event appears as "Purchase" separate from "purchase"   | Wrong case                                                   | Use the exact lowercase GA4 names                                               |
| Multi-currency store (Shopify Markets) totals look off | Shop currency sent while customer paid in another            | Send the currency the customer paid in; GA4 converts to the property's currency |
| Purchases collapse into fewer transactions             | `transaction_id` missing or reused                           | Use the Shopify order ID; never a constant                                      |

**Worked example:** a theme-based `view_item` tag used `value: {{ product.price }}`. A $29 product was reported as $2,900 in item revenue. Changing it to `{{ product.price | divided_by: 100.0 }}` fixed new data.



## Sources

* [Analyzify: Google's native Shopify server-side integration](https://analyzify.com/hub/google-shopify-server-side-integration)
* [Shopify.dev: Standard events](https://shopify.dev/docs/api/web-pixels-api/standard-events)
* [Shopify.dev: checkout\_completed](https://shopify.dev/docs/api/web-pixels-api/standard-events/checkout_completed)
* [Lionel Z.: Shopify pixel consent defaults](https://lionelz.com/en/blog/shopify-ga4-meta-capi-sin-app/)
* [Shopify Community: sandbox URLs in GA4 page paths](https://community.shopify.com/t/shopify-pixel-customer-events/324726/7)
* [Owntag: the gcs consent parameter](https://owntag-website.pages.dev/blog/gsc-parameter)
* [Shopify.dev: Test Shopify Payments (test cards)](https://shopify.dev/docs/api/commerce-components/pay/test-shop-pay.md)
* [Shopify.dev: Customer Privacy API](https://shopify.dev/docs/api/customer-privacy)



***

Next: Part 6: Fixing duplicate, misattributed and mismatched data →
