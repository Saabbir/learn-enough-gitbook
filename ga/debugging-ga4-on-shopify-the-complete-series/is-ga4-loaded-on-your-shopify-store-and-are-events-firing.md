# Is GA4 Loaded on Your Shopify Store, and Are Events Firing?

{% hint style="info" %}
_Part 3 of 6 in the Debugging GA4 on Shopify series._
{% endhint %}

This part answers the two questions every GA4 debugging session starts with: is GA4 actually loaded on the store, and are events firing when shoppers do things? You'll check from the admin, the page source, the console and the Network tab, then test events one by one, including custom events of your own.

> **New to this series?** GA4 on Shopify can run in two places: on the page itself (theme code) or inside Shopify's pixel sandbox (custom pixels and app pixels). It is also either _explicit_ (code your team wrote and can edit) or _implicit_ (managed by Shopify, Google or an app). Part 1 shows how to tell which setup you have.

### Use case 1: Is GA loaded properly on the site?

GA is loaded properly when a `collect` request with _your_ Measurement ID leaves the browser on every page and your visit shows up in GA4 Realtime. Seeing the script tag alone proves nothing. Run these six checks in order.

**Typical triggers:** Realtime shows 0 users; a client asks "is GA even installed?"; you just switched themes, installed a cookie banner or reinstalled the Google & YouTube app.

#### 1. Check the admin for every GA source

1. Settings → Customer events: note every app pixel and custom pixel and whether each says **Connected**.
2. Sales channels → Google & YouTube → Settings: is a Google Analytics property connected, and which one?
3. Online Store → Themes → Edit code: search `theme.liquid` (and snippets) for `G-`, `GTM-` and `gtag`.
4. Themes → Customize → App embeds: is a tracking app's embed switched on?

#### 2. Check the page source

Open any storefront page, press Ctrl+U (Cmd+Option+U on Mac) and search for `gtag/js`, `gtm.js`, `G-` and `GTM-`.

Note: the Google & YouTube app, custom pixels and tracking apps aren't in your theme files, so finding no Google code here doesn't mean GA is missing. Rely on the admin (check 1) and the Network tab (check 4) for those setups.

#### 3. Check from the console (top frame)

Paste this into DevTools → Console on a storefront page:

```javascript
// 1. Is gtag defined on the page?
console.log('gtag:', typeof window.gtag);            // "function" = gtag.js loaded in the theme

// 2. Which Google tag / GTM IDs are live?
console.log('tags:', Object.keys(window.google_tag_manager || {}));
// e.g. ["G-AB12CD34EF", "GTM-XYZ123"]  -> two IDs can mean double tagging

// 3. What has been pushed to the dataLayer?
console.table((window.dataLayer || []).map(e => e[0] || e.event));

// 4. Every Google script on the page
console.log([...document.scripts].map(s => s.src)
  .filter(s => /googletagmanager|google-analytics/.test(s)));
```

#### 4. Check the Network tab (the decisive test)

1. DevTools → Network, tick **Preserve log** and **Disable cache**.
2. Type `collect?v=2` in the filter box and reload.
3. You should see a request like `https://www.google-analytics.com/g/collect?v=2&tid=G-AB12CD34EF&...&en=page_view` with status **204**.
4. Click it and check the payload: `tid` = your Measurement ID, `en` = `page_view`, `dl` = the real page URL.

Not every store sends to `google-analytics.com`. Server-side setups send the same kind of request to the store's own subdomain, such as `https://signal.example-store.com/g/collect`. The `collect?v=2` filter catches both; see "When hits go to your own subdomain" in Part 1.

#### 5. Check the sandbox (pixel setups)

* Open the Shopify Pixel Helper (Settings → Customer events → click the pixel → **Test**). Each pixel's `page_viewed` event should show a green dot.
* "Pixel is awaiting consent" means consent hasn't been given yet. Accept the cookie banner or click **Give consent to continue test** ([Shopify](https://help.shopify.com/en/manual/promoting-marketing/pixels/custom-pixels/testing)).
* For a custom pixel, pick its frame in the console's context selector (names start with `web-pixel-sandbox`) and run `typeof gtag`. It should return `"function"`.

#### 6. Confirm inside GA4

Open GA4 → Reports → Realtime. Within about a minute you should see 1 user from your city with a `page_view` event. If Network shows 204 hits but Realtime stays empty, check Admin → Data filters for an **Active** internal-traffic filter that excludes your IP, and confirm you're looking at the property that matches `tid`.

#### Reading the result

| What you see                                                            | What it means                                                                       | Next step                                            |
| ----------------------------------------------------------------------- | ----------------------------------------------------------------------------------- | ---------------------------------------------------- |
| Script loads, `collect` returns 204 with your `tid`, Realtime shows you | **GA is loaded properly**                                                           | Move on to event checks                              |
| No Google script anywhere, no `collect` hits, no pixel in Pixel Helper  | GA isn't installed, or the app/pixel is disconnected                                | Reconnect in Google & YouTube or Customer events     |
| `gtag/js` or `gtm.js` loads but no `collect` request                    | Consent denied, no GA4 config tag in the GTM container, or a JS error before config | Use case 5; check GTM tags; check Console for errors |
| `collect` hits carry a different `tid` than expected                    | Data is going to the wrong property                                                 | Use case 8                                           |
| Two `page_view` hits per page load                                      | Double tagging                                                                      | Use case 4                                           |
| Request status `(blocked:other)` or `ERR_BLOCKED_BY_CLIENT`             | Your ad blocker, not the site                                                       | Retest in a clean profile                            |
| `dl=` contains `/wpm@…/sandbox/`                                        | Custom pixel is sending the iframe URL                                              | Use case 6                                           |

#### Worked examples

**Example A: GTM in the theme, nothing in GA4.** The console shows `["GTM-XYZ123"]` but no `G-` ID, and Network shows `gtm.js` loading but no `collect`. The container was published without a GA4 Google tag. Fix: in GTM add a Google tag with your `G-` ID on the _Initialization – All Pages_ trigger, then publish.

**Example B: Google & YouTube app, "there's no GA code in the theme".** The client searched the theme and found nothing. But Google & YouTube → Settings shows the GA4 property connected, Pixel Helper lists the Google & YouTube pixel, and Network shows `collect` hits with the right `tid`. GA is loaded properly, just implicitly. A test order's `purchase` doesn't appear in Network, which is expected: it's sent from Shopify's servers, so confirm it in GA4 Realtime.

### Checking that GA events fire (page\_view and custom events)

An event is firing when, at the moment of the action, a `collect` hit with `en=<event name>` leaves the browser and GA4 shows that event in DebugView or Realtime. Check three layers in order: did Shopify register the action, did a hit go out, did GA4 receive it.

#### 1. Run a test script of actions

Do one action at a time and confirm one new hit for each:

| Action                                   | GA4 event expected       | Shopify event behind it (pixel setups) |
| ---------------------------------------- | ------------------------ | -------------------------------------- |
| Load any page                            | `page_view`              | `page_viewed`                          |
| Open a collection                        | `view_item_list`         | `collection_viewed`                    |
| Open a product                           | `view_item`              | `product_viewed`                       |
| Search                                   | `search`                 | `search_submitted`                     |
| Add to cart                              | `add_to_cart`            | `product_added_to_cart`                |
| Open the cart                            | `view_cart`              | `cart_viewed`                          |
| Start checkout                           | `begin_checkout`         | `checkout_started`                     |
| Submit shipping                          | `add_shipping_info`      | `checkout_shipping_info_submitted`     |
| Submit payment                           | `add_payment_info`       | `payment_info_submitted`               |
| Complete a test order                    | `purchase`               | `checkout_completed`                   |
| Your own action (newsletter, quiz, etc.) | e.g. `newsletter_signup` | A custom event you publish             |

#### 2. Watch the hits leave the browser

Use the Network tab filtered to `collect?v=2`, or Omnibug. GA4 often bundles several events into one request. When it does, they're in the request body (DevTools → Payload), one event per line, not in the URL ([Thyngster](https://hl.thyngster.com/?p=2889)).

How to read a hit:

| Parameter     | Meaning                                 | Example                                  |
| ------------- | --------------------------------------- | ---------------------------------------- |
| `tid`         | Measurement ID                          | `G-AB12CD34EF`                           |
| `en`          | Event name                              | `add_to_cart`                            |
| `ep.<name>`   | Text parameter                          | `ep.form_location=footer`                |
| `epn.<name>`  | Number parameter                        | `epn.value=29`                           |
| `cu`          | Currency                                | `USD`                                    |
| `pr1`, `pr2`… | One item each                           | `pr1=idTEE-BLK-M~nmClassic Tee~pr29~qt1` |
| `dl`          | Page URL                                | should be the real URL, not `/wpm@…`     |
| `_dbg`        | Debug mode on (event goes to DebugView) | `_dbg=1`                                 |
| `gcs`         | Consent state                           | `G111` = granted (Use case 5)            |

The `ep.`/`epn.` prefixes and `_dbg` come from [Simo Ahava's GA4 event guide](https://simoahava.com/analytics/implementation-guide-events-google-analytics-4).

#### 3. Confirm GA4 received it

Open DebugView (needs debug mode, Use case 2) or Realtime and look for the event name within a minute. For stores on the Google & YouTube app, check `purchase` here, because since July 2026 it's sent from Shopify's servers and won't show in the browser (Part 1).

#### 4. For pixel setups, check the Shopify side too

If no hit appears, find out whether Shopify even delivered the event to your pixel:

* **Shopify Pixel Helper:** each event under _Events received_ should have a green dot. A red dot means your pixel code threw an error; expand it to read the error ([Shopify](https://help.shopify.com/en/manual/promoting-marketing/pixels/custom-pixels/testing)).
* **Log every event:** temporarily add this to the custom pixel, then read it in the sandbox's console context:

```javascript
analytics.subscribe('all_standard_events', (event) => {
  console.log(event.name, event.data);
});
```

Pixel Helper clears its log on each page load, so check each event before you move on.

#### Example: tracking a custom event three ways

Goal: send `newsletter_signup` with `form_location` when the footer form is submitted.

**Direct gtag.js** (theme code):

```javascript
gtag('event', 'newsletter_signup', { form_location: 'footer' });
```

**GTM** (theme code + container): push to the dataLayer, then in GTM create a _Custom Event_ trigger for `newsletter_signup` and a _GA4 Event_ tag that sends `form_location`.

```javascript
window.dataLayer = window.dataLayer || [];
dataLayer.push({ event: 'newsletter_signup', form_location: 'footer' });
```

**Custom pixel** (theme publishes, pixel subscribes): theme code can't reach the sandbox directly, so publish a Shopify custom event and pick it up in the pixel. Its data arrives on `event.customData` ([Shopify.dev](https://shopify.dev/docs/api/web-pixels-api/emitting-data)).

```javascript
// In the theme (e.g. the form's submit handler)
Shopify.analytics.publish('newsletter_signup', { form_location: 'footer' });

// In the custom pixel
analytics.subscribe('newsletter_signup', (event) => {
  gtag('event', 'newsletter_signup', {
    form_location: event.customData.form_location,
    page_location: event.context.document.location.href
  });
});
```

In all three cases you should see one hit with `en=newsletter_signup` and `ep.form_location=footer`. The event shows in DebugView right away. To use `form_location` in standard reports, register it under GA4 → Admin → Custom definitions.

### Sources

* [Shopify Help Center: Testing custom pixels](https://help.shopify.com/en/manual/promoting-marketing/pixels/custom-pixels/testing)
* [Thyngster: GA4 hit parameters](https://hl.thyngster.com/?p=2889)
* [Simo Ahava: GA4 event implementation guide](https://simoahava.com/analytics/implementation-guide-events-google-analytics-4)
* [Shopify.dev: Emitting custom events](https://shopify.dev/docs/api/web-pixels-api/emitting-data)
* [Shopify.dev: Standard events](https://shopify.dev/docs/api/web-pixels-api/standard-events)
* [Owntag: the gcs consent parameter](https://owntag-website.pages.dev/blog/gsc-parameter)



***

Next: Part 4: GTM on Shopify and DebugView →
