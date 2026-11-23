---
icon: '6'
---

# Fixing Duplicate, Misattributed and Mismatched GA4 Data on Shopify

{% hint style="info" %}
_Part 6 of 6 in the Debugging GA4 on Shopify series._
{% endhint %}

The last part deals with data that arrives but can't be trusted: events counted twice, data landing in the wrong property, sales credited to PayPal or Shop Pay instead of the campaign that earned them, and GA4 revenue that never matches Shopify. It ends with a checklist to run after every theme, app or checkout change.

> **New to this series?** GA4 on Shopify can run in two places: on the page itself (theme code) or inside Shopify's pixel sandbox (custom pixels and app pixels). It is also either _explicit_ (code your team wrote and can edit) or _implicit_ (managed by Shopify, Google or an app). Part 1 shows how to tell which setup you have.



## Use case 4: Duplicate page views or purchases

Doubled numbers almost always mean two installations are sending the same event. The fix is one source per event, not a filter in GA4.

**Symptoms:** GA4 revenue is roughly 2x Shopify's; Views per session looks unusually high; Network shows two `en=page_view` hits per page load; one transaction ID appears twice in reports.

| Duplicate source                                                                                                           | How to confirm                                                                                                    | Fix                                                                |
| -------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| Google & YouTube app **plus** gtag/GTM in the theme                                                                        | Two `collect` hits per page; Pixel Helper lists the Google & YouTube pixel while page source also has `gtag/js`   | Keep one: disconnect GA4 in the app, or remove the theme code      |
| Custom pixel **plus** app pixel both sending `purchase`                                                                    | Two `en=purchase` hits on the thank-you page, often with different `transaction_id`s (order ID vs checkout token) | Turn off purchase in one of them                                   |
| `gtag('config')` sends an automatic page\_view **and** the code also sends one manually                                    | Two page\_view hits from the same frame                                                                           | Set `send_page_view: false` in config                              |
| GA4 Enhanced measurement "page changes based on browser history" **plus** manual page\_view on themes with AJAX navigation | Extra page\_view on filter or variant changes                                                                     | Turn off that Enhanced measurement option, or stop the manual one  |
| Two different `G-` IDs both live                                                                                           | `Object.keys(google_tag_manager)` shows two G- IDs                                                                | Remove the old ID, or route both through one Google tag on purpose |

**A newer duplicate to watch for:** the Google & YouTube app's server-side purchase plus a custom pixel or GTM tag sending `purchase` to the same property. Google deduplicates by transaction ID, so the copies only merge if both send the same ID. A pixel that sends the checkout token while the app sends the order ID will double count.

**Find duplicated orders in GA4:** Explore → Free form, rows = _Transaction ID_, values = _Ecommerce purchases_, sort descending. Any row with 2 or more is a duplicate. GA4 can drop repeats that share a transaction ID, but don't rely on it: two sources usually send different IDs.

**Worked example:** a merchant installed the Google & YouTube app for Google Ads, but an agency's GTM container in `theme.liquid` was already sending GA4 to the same property. Purchases were tracked once (only the app covered checkout), but page views doubled and engagement rate looked inflated. Disconnecting GA4 inside the app (keeping the Ads link) fixed it.



## Use case 8: Data goes to the wrong property

If hits leave the browser but your GA4 property stays empty, compare the `tid` in the `collect` request with your property's Measurement ID. A single wrong character means zero data ([Analytics Agent](https://analytics-agent-marketing-site.pages.dev/resources/fix-shopify-tracking/)).

**Symptoms:** Network shows 204 hits, Realtime shows nothing; data shows up in an old, staging or agency property; numbers split between two properties.

**Checks:**

1. GA4 → Admin → Data streams → your web stream: copy the **Measurement ID** (`G-XXXXXXXXXX`).
2. Compare it character by character with: the `tid` in Network, the ID in Google & YouTube app settings, the ID in GTM's Google tag, and any ID in custom pixel code.
3. Run `Object.keys(window.google_tag_manager || {})` to spot extra IDs nobody remembers adding.
4. If you see a `GT-` ID instead of `G-`, that's a combined Google tag (often created by the Google & YouTube app). It's fine as long as its destinations include your GA4 property: check Admin → Data streams → Configure tag settings → Manage Google tag.

**Worked example:** a store rebuilt its GA4 property but the Google & YouTube app was still connected to the old one. The new property showed only storefront page views (from GTM), while all purchases went to the old property. Reconnecting the app to the new property, or disconnecting it and tracking checkout with a custom pixel, brought them together.



## Use case 9: Sales credited to PayPal, Shop Pay or your own domain

When a shopper leaves for a payment page and comes back, GA4 can start a new session with that payment site as the source, stealing credit from the real campaign. Shop Pay Installments, for example, redirects to `shop.app` ([Analytics Agent](https://analytics-agent-marketing-site.pages.dev/resources/fix-shopify-tracking/)).

**Symptoms:** `paypal.com / referral`, `shop.app / referral`, `klarna.com / referral` or `yourstore.myshopify.com / referral` among top purchase sources; Google Ads conversions in GA4 lower than in Ads; many purchases with `(direct) / (none)`.

**Check:** Explore → Free form, rows = _Session source / medium_, values = _Ecommerce purchases_ and _Purchase revenue_. Look for payment providers and your own domains.

**Fixes:**

1. GA4 → Admin → Data streams → Configure tag settings → **List unwanted referrals**: add each payment domain you use, e.g. `paypal.com`, `shop.app`, `klarna.com`, `afterpay.com`, `stripe.com`.
2. Same screen → **Configure your domains**: add your primary domain and `yourstore.myshopify.com` if shoppers can land on both.
3. In Shopify, set the primary domain to redirect all traffic (Settings → Domains), so sessions don't hop between `.myshopify.com` and your domain.

**Worked example:** after adding PayPal Express, `paypal.com / referral` became the #2 revenue source overnight, while Google / cpc revenue fell by the same amount. Adding `paypal.com` to unwanted referrals restored attribution for new sessions. Past data isn't corrected.



## Use case 10: GA4 revenue doesn't match Shopify

GA4 will never match Shopify exactly. Ad blockers, declined consent, Safari's cookie limits and shoppers who close the tab before the thank-you page all lose some orders, so GA4 running somewhat below Shopify is normal. A gap is a problem when it's large, sudden, or concentrated in one segment.

**Compare like with like first:**

1. **Time zone:** GA4 property (Admin → Property details) vs Shopify store (Settings → General). A 6-hour difference moves orders between days.
2. **Count before money:** compare the number of orders to GA4 _Transactions_ before comparing revenue.
3. **Channels:** Shopify totals include POS, draft orders, subscription renewals and marketplace orders that never pass through the online checkout. Filter Shopify to the Online Store channel.
4. **What "revenue" includes:** Shopify net sales subtract returns and discounts; GA4 purchase revenue is what the pixel sent at purchase time, usually including tax and shipping unless you excluded them.
5. **Currency:** both in the same currency.

**Then find which orders are missing.** Export order IDs from Shopify (Orders → Export) and transaction IDs from GA4 (Explore → Free form, rows = Transaction ID). Match them in a spreadsheet and look for a pattern in the missing ones:

| Missing orders share…                         | Likely cause                                                     | See                                     |
| --------------------------------------------- | ---------------------------------------------------------------- | --------------------------------------- |
| One payment method (PayPal, Klarna, Shop Pay) | Redirect to an outside page; pixel never sees the thank-you page | Use case 3 and 9                        |
| EU/UK billing addresses                       | Consent not granted, or pixel permission too strict              | Use case 5                              |
| A date onward                                 | A theme change, app install or checkout migration that day       | Use case 1 and 3                        |
| Safari / iOS                                  | Browser privacy limits                                           | Expected; consider server-side tracking |
| No pattern, small gap                         | Normal loss                                                      | None                                    |

Extra GA4 transactions (more than Shopify) point to duplicates: see Use case 4.



## Quick checklist: run after every theme, app or checkout change

* [ ] Customer events: every GA-related pixel shows **Connected**, and each has the consent permission you intend
* [ ] Only one source sends each GA4 event (no app + theme + pixel overlap)
* [ ] Home page: one `collect` hit with `en=page_view` and the correct `tid`, status 204
* [ ] `dl=` shows the real URL, not `/wpm@…/sandbox/`
* [ ] Product page: `view_item` with `items`, `value` and `currency`
* [ ] Add to cart: `add_to_cart` fires once
* [ ] Checkout: `begin_checkout`, `add_shipping_info`, `add_payment_info` fire
* [ ] Test order (Shopify Payments test mode): exactly one `purchase` with a real `transaction_id`, numeric `value` and `currency`
* [ ] Consent: rejected = no hits or `gcs=G100`; accepted = hits with `gcs=G111`
* [ ] GA4 Realtime or DebugView shows the test session and the purchase
* [ ] Unwanted referrals list includes every payment provider you use
* [ ] Test orders cancelled and archived in Shopify



## Sources

* [Analytics Agent: Fix Shopify GA4 tracking](https://analytics-agent-marketing-site.pages.dev/resources/fix-shopify-tracking/)
