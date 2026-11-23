---
icon: '4'
---

# Google Tag Manager on Shopify, and Why DebugView Stays Empty

{% hint style="info" %}
_Part 4 of 6 in the Debugging GA4 on Shopify series._
{% endhint %}

On many Shopify stores GA4 runs through Google Tag Manager, and on Shopify GTM can live in two places that behave very differently: the theme and a custom pixel. This part covers how to debug each one, how GTM fits alongside the Google & YouTube app, and why GA4 DebugView so often stays empty on Shopify even when events are arriving.

> **New to this series?** GA4 on Shopify can run in two places: on the page itself (theme code) or inside Shopify's pixel sandbox (custom pixels and app pixels). It is also either _explicit_ (code your team wrote and can edit) or _implicit_ (managed by Shopify, Google or an app). Part 1 shows how to tell which setup you have.



## GTM on Shopify: what's different

On many stores GA4 is loaded through Google Tag Manager, and on Shopify one container can live in two places that behave very differently. In the theme it sees the storefront page (clicks, forms, scroll) but not checkout. In a custom pixel it sees checkout and purchase, but only through the events Shopify publishes ([Trackingplan](https://www.trackingplan.com/blog/google-tag-manager-shopify)).

|                             | GTM in the theme                          | GTM in a custom pixel                                                                                                                                                                           |
| --------------------------- | ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| What it can see             | The real page: DOM, clicks, forms, scroll | Only Shopify's published events (standard + custom)                                                                                                                                             |
| Checkout and Thank you page | No                                        | Yes                                                                                                                                                                                             |
| GTM Preview / Tag Assistant | Works                                     | Not available on checkout ([Analyzify](https://docs.analyzify.com/debug-checkout-events)); Tag Assistant may not detect it ([Stape](https://stape.io/blog/shopify-google-analytics-4-tracking)) |
| Page URL sent to GA4        | Real URL                                  | Sandbox URL unless you override `page_location` (Use case 6)                                                                                                                                    |
| `dataLayer`                 | The page's own                            | A separate one inside the sandbox iframe                                                                                                                                                        |
| Google's position           | Standard setup                            | Google tags in a custom pixel are "not a supported implementation" ([Google](https://support.google.com/tagmanager/answer/15642481))                                                            |

#### Debugging GTM in the theme

1. In GTM, click **Preview**, enter your store URL and connect. Tag Assistant opens the store in debug mode.
2. For each event in the left column (Container Loaded, your custom events), check **Tags Fired** vs **Tags Not Fired**, and the **Data Layer** and **Variables** tabs.
3. Your GA4 **Google tag** should fire on _Initialization – All Pages_; each GA4 Event tag should fire on its trigger.
4. If Preview works but the live site doesn't, the container probably isn't published. Check **Versions** for the live version and its date.

#### Debugging GTM in a custom pixel

Preview mode won't help here, so use Pixel Helper, console logs inside the sandbox, and the Network tab or Omnibug. A typical setup pushes Shopify events into the sandbox dataLayer:

```javascript
// Settings → Customer events → custom pixel (no <script> tags)
window.dataLayer = window.dataLayer || [];
(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
})(window,document,'script','dataLayer','GTM-XXXXXXX');

analytics.subscribe('checkout_completed', (event) => {
  const c = event.data.checkout;
  window.dataLayer.push({ ecommerce: null });          // clear the previous ecommerce object
  window.dataLayer.push({
    event: 'purchase',
    page_location: event.context.document.location.href,
    ecommerce: {
      transaction_id: c.order?.id,
      value: c.totalPrice?.amount,
      currency: c.currencyCode,
      items: c.lineItems.map(li => ({
        item_id: li.variant?.sku, item_name: li.title,
        price: li.variant?.price?.amount, quantity: li.quantity
      }))
    }
  });
});
```

In GTM, the matching GA4 Event tag (`purchase`) should read ecommerce data from the dataLayer and set `page_location` from a Data Layer Variable. Shopify's own walkthrough is [Create a Google Tag Manager custom pixel](https://help.shopify.com/en/manual/promoting-marketing/pixels/custom-pixels/gtm-tutorial).

**What to check when it fails:**

* Pixel Helper shows `checkout_completed` green but no `collect` hit → the GTM trigger or tag doesn't match the pushed event name, or consent blocks the tag.
* Pushes visible in the sandbox console but the GA4 tag sends the sandbox URL → `page_location` not overridden on that tag.
* Events arrive twice → GTM in the theme _and_ in the pixel both send the same event (Use case 4).

#### GTM and the Google & YouTube app together

You can't set up GTM through the Google & YouTube app, and Google advises moving Google tags out of GTM containers on Shopify into the app ([Google](https://support.google.com/tagmanager/answer/15642481)). If you keep both, give each event exactly one source: for example, the app sends the GA4 ecommerce events, and GTM in the theme sends only custom events and non-Google tags.



## Use case 2: GA loads, but DebugView is empty

DebugView only shows events that carry the `debug_mode` flag, and on Shopify the usual way of setting it (the GA Debugger extension or Tag Assistant) can't reach code inside the pixel sandbox. Events are often arriving fine; you just can't see them there ([WeltPixel](https://weltpixel.com/blogs/news/ga4-debugview-shopify-verify-events-firing)).

**Symptom:** Network shows `collect` hits returning 204, Realtime shows events, but Admin → DebugView says "No devices available".

**Check:** open a `collect` request and look for `_dbg=1` in the query string. If it's missing, the hit isn't flagged for DebugView.

**Fix, by setup:**

| Setup                | How to turn on debug mode                                                                                                              |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| gtag.js in the theme | Connect the site in Tag Assistant, or temporarily add `debug_mode: true` to the `gtag('config', ...)` call                             |
| GTM in the theme     | Open GTM Preview; tags fired in Preview are flagged automatically                                                                      |
| Custom pixel         | Add `debug_mode: true` to the config inside the pixel code (example below), save, test, then remove it                                 |
| Third-party app      | Use the app's own debug toggle (most have one in advanced settings)                                                                    |
| Google & YouTube app | No debug switch. Verify with the Network tab and GA4 Realtime instead; purchases are sent server-side, so check those in Realtime only |

**Example: a custom pixel with debug mode on**

```javascript
// Settings → Customer events → Add custom pixel
const MEASUREMENT_ID = 'G-AB12CD34EF';

const s = document.createElement('script');
s.src = `https://www.googletagmanager.com/gtag/js?id=${MEASUREMENT_ID}`;
s.async = true;
document.head.appendChild(s);

window.dataLayer = window.dataLayer || [];
function gtag(){ dataLayer.push(arguments); }
gtag('js', new Date());
gtag('config', MEASUREMENT_ID, {
  send_page_view: false,   // we send page_view ourselves below
  debug_mode: true         // REMOVE after testing
});

analytics.subscribe('page_viewed', (event) => {
  gtag('event', 'page_view', {
    page_location: event.context.document.location.href,
    page_referrer: event.context.document.referrer,
    page_title: event.context.document.title
  });
});
```

Other reasons DebugView stays empty: you're in a different GA4 property than the `tid`; the device selector at the top of DebugView is set to another device; or more than about 30 minutes have passed since your last debug event.



## Sources

* [Trackingplan: Google Tag Manager on Shopify](https://www.trackingplan.com/blog/google-tag-manager-shopify)
* [Analyzify: Debug checkout events](https://docs.analyzify.com/debug-checkout-events)
* [Stape: Shopify GA4 tracking](https://stape.io/blog/shopify-google-analytics-4-tracking)
* [Google: Migrate your Google tags with the Google & YouTube app on Shopify](https://support.google.com/tagmanager/answer/15642481)
* [Shopify Help Center: Create a Google Tag Manager custom pixel](https://help.shopify.com/en/manual/promoting-marketing/pixels/custom-pixels/gtm-tutorial)
* [WeltPixel: GA4 DebugView on Shopify](https://weltpixel.com/blogs/news/ga4-debugview-shopify-verify-events-firing)



***

Next: Part 5: Fixing missing checkout and ecommerce data →
