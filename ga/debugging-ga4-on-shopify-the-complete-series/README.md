# 📚 Debugging GA4 on Shopify: The Complete Series

GA4 on Shopify runs in two separate places: the **storefront** (theme pages, where normal gtag.js or GTM works) and Shopify's **sandboxed web pixels** under Customer Events (which run on storefront pages and checkout alike, and hide from most browser debug tools). Almost every "GA is broken" ticket comes down to not knowing which of the two is sending a given event.

This six-part series walks through debugging GA4 on Shopify from start to finish: how GA4 gets onto a store, the tools to watch it, how to confirm it's loaded and firing, how GTM changes things, and how to fix the most common problems. Each part stands on its own, so start with the one that matches your problem, or read them in order.



## The series



1. **How GA4 Gets Onto a Shopify Store: gtag, GTM, Apps and Custom Pixels**: the five ways GA4 is loaded, explicit vs implicit, and server-side setups that send hits to your own subdomain.
2. **Tools and Chrome Extensions for Debugging GA4 on Shopify**: the Network tab, Omnibug, Tag Assistant, Shopify Pixel Helper and more, and what each can't see.
3. **Is GA4 Loaded on Your Shopify Store, and Are Events Firing?**: six checks to confirm GA4 is loaded, and how to test page views, ecommerce events and custom events.
4. **Google Tag Manager on Shopify, and Why DebugView Stays Empty**: GTM in the theme vs a custom pixel, GTM with the Google & YouTube app, and empty DebugView.
5. **Fixing Missing Checkout and Ecommerce Data in GA4 on Shopify**: missing purchases, consent banners, sandbox page paths and broken ecommerce values.
6. **Fixing Duplicate, Misattributed and Mismatched GA4 Data on Shopify**: duplicates, wrong property, payment-provider referrals, revenue that doesn't match, and a final checklist.



## Not sure where to start?

Open your store, then DevTools → Network, filter `collect?v=2` and reload. Answer these questions in order; the first "no" tells you which part to read.



1. **Any `collect` hit?** No → GA isn't loaded: check the install, pixel status and consent (Part 3, Part 5).
2. **Does `tid` match your `G-` ID?** No → data is going to the wrong property (Part 6).
3. **One hit per event, with the real page URL?** No → duplicates (Part 6) or sandbox URLs (Part 5).
4. **Does a test purchase arrive with value and currency?** No → checkout or ecommerce parameters (Part 5).
5. All yes → GA works end to end. For remaining gaps see DebugView (Part 4) and attribution or revenue gaps (Part 6).



## Quick checklist

Run this after every theme, app or checkout change. Each item is explained in the series.



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



***

Start with Part 1: How GA4 gets onto a Shopify store →
