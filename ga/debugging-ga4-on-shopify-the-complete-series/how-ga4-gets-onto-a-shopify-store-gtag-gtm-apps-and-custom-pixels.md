# How GA4 Gets Onto a Shopify Store: gtag, GTM, Apps and Custom Pixels

{% hint style="info" %}
_Part 1 of 6 in the Debugging GA4 on Shopify series._
{% endhint %}

When GA4 "isn't working" on a Shopify store, the first question isn't what broke. It's how GA4 got there in the first place. Shopify stores load GA4 in at least five different ways, often two at once, and each one fails differently and needs different tools to debug. This part shows you how to identify every way GA4 is loaded on a store, including setups that leave no trace in the theme code.

## How is GA loaded on this store?

GA4 reaches a Shopify store in one of five ways, and you must find every one that's active before debugging, because each is checked with different tools. Two questions classify each one:

* **Who controls the code?** **Explicit** loading is GA code your team wrote and can read and edit: a gtag.js or GTM snippet in the theme, or a custom pixel you wrote (for example, one that loads GTM only on checkout pages). **Implicit** (managed) loading is GA loaded by Shopify, Google or an app with code you don't write or edit: the Google & YouTube app, an app pixel, or a tracking service such as Leaf Signal.
* **Where does it run?** On the page itself (theme code) or inside Shopify's pixel sandbox (custom pixels and app pixels). This decides which debugging tools can see it.

| Method                                                                           | Explicit or implicit                 | Where it's configured                                           | Where the code runs                                                             | Covers checkout? |
| -------------------------------------------------------------------------------- | ------------------------------------ | --------------------------------------------------------------- | ------------------------------------------------------------------------------- | ---------------- |
| Direct gtag.js in the theme                                                      | Explicit                             | Online Store → Themes → Edit code (`theme.liquid` or a snippet) | The page itself (top window)                                                    | No               |
| GTM in the theme, with a GA4 Google tag inside the container                     | Explicit                             | `theme.liquid` + the GTM container                              | The page itself                                                                 | No               |
| Google & YouTube app                                                             | Implicit                             | Sales channels → Google & YouTube → Settings                    | Managed by Shopify and Google; purchases also sent server-to-server (see below) | Yes              |
| Custom pixel that loads gtag.js or GTM                                           | Explicit (your code, in the sandbox) | Settings → Customer events → Custom pixels                      | Lax sandbox (an iframe)                                                         | Yes              |
| Third-party or agency tracking app (Elevar, Analyzify, Stape, Leaf Signal, etc.) | Implicit                             | The app + Themes → Customize → App embeds + an app pixel        | Varies; often browser hits to your own subdomain, then a server                 | Yes              |

**Pixels aren't checkout-only.** A custom pixel or app pixel also runs on the homepage, collection pages (PLP) and product pages (PDP). GTM in a custom pixel, for example, runs on storefront, checkout, Thank you and Order status pages ([Trackingplan](https://www.trackingplan.com/blog/google-tag-manager-shopify)). On those pages it hears `page_viewed`, `collection_viewed`, `product_viewed`, `product_added_to_cart` and any custom events the theme publishes. What it can't do is touch the page: custom pixels can't paint UI, can't read the DOM, and don't see the main window's real URL ([Lionel Z.](https://lionelz.com/en/blog/shopify-ga4-meta-capi-sin-app/)). So a pixel can fire `view_item` on a PDP, but it can't track a click on a size-guide button unless the theme publishes that click as a custom event.

Three facts that explain most "where did this tag come from?" mysteries:

* **Old Preferences tags became custom pixels.** Shopify deprecated the Google Analytics field under Online Store → Preferences. After February 2, 2025, Shopify converted untouched tags into custom pixels ([Google](https://support.google.com/tagmanager/answer/15642481)). A custom pixel nobody remembers creating is often that old tag.
* **Legacy checkout scripts stop running.** `checkout.liquid` steps were sunset on August 13, 2024 ([Elevar](https://getelevar.com/shopify/how-to-upgrade-from-checkout-liquid-to-checkout-extensibility/)). The Thank you and Order status page upgrade deadline was August 28, 2025 for Shopify Plus and August 26, 2026 for other plans; Additional scripts there no longer run ([Google](https://support.google.com/tagmanager/answer/15642481)).
* **The Google & YouTube app now sends purchases from Shopify's servers.** Since July 2026, purchase events go straight from Shopify to GA4 through Google's Data Manager API, bypassing the browser ([Two Owls](https://twoowls.io/blogs/shopify-ga4-server-to-server-integration/)). On August 17, 2026 the app added `view_item_list`, `remove_from_cart`, `view_cart` and `add_shipping_info` ([PPC Land](https://ppc.land/shopify-ga4-tracking-gains-4-events-august-17-only-purchase-is-server-side/)). Practical effect: you won't see that purchase in the browser's Network tab.

### How to identify each method

**1. Direct gtag.js (explicit).** The theme contains a snippet like this:

```html
<script async src="https://www.googletagmanager.com/gtag/js?id=G-AB12CD34EF"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-AB12CD34EF');
</script>
```

Confirm it: in Edit code, search all theme files for `gtag/js`. On the live page, DevTools → Network shows `gtag/js?id=G-…` and its **Initiator** column is the page document.

**2. GTM (explicit).** The theme contains `googletagmanager.com/gtm.js?id=GTM-XXXXXXX`, usually with a `ns.html?id=GTM-` noscript iframe after `<body>`. In Network you'll see `gtm.js?id=GTM-…` load first. If the container holds a GA4 Google tag, `gtag/js?id=G-…` loads next and its **Initiator is `gtm.js`**. That Initiator column is the quickest way to tell "GA via GTM" from "GA hardcoded".

**3. Google & YouTube app (implicit).** Theme files contain no Google code. Sales channels → Google & YouTube → Settings shows a connected GA4 property, and Settings → Customer events lists the Google & YouTube pixel. Network still shows `collect` hits with your `G-` ID. You can't set up GTM through this app ([Google](https://support.google.com/tagmanager/answer/15642481)).

**4. Custom pixel (explicit, runs in the sandbox).** Open each pixel under Settings → Customer events → Custom pixels and look for `gtag/js` or `gtm.js` in its code. On the storefront, the Shopify Pixel Helper lists it, and GA hits may carry a `/wpm@…/sandbox/` page path. Note that Google calls Google tags in a custom pixel "not a supported implementation" ([Google](https://support.google.com/tagmanager/answer/15642481)), so Google support won't help debug it.

A common explicit pattern is a custom pixel that loads GTM only on checkout, guarded by a check like `if (window.location.pathname.includes("/checkouts/"))`, while something else tracks the storefront. Inside the sandbox, `window.location` is the sandbox frame's address, not the page's. A check like this can still match, because the sandbox address seen in GA4 page paths ends with the page's own path (for example `/wpm@…/sandbox/modern/cart`, [Shopify Community](https://community.shopify.com/t/shopify-pixel-customer-events/324726/7)). The real page URL comes from `event.context.document.location` in each event, so that's the safer thing to test.

A custom pixel created by Shopify's automatic conversion of an old Preferences tag also counts as explicit: nobody on your team wrote it, but the code is visible and editable.

**5. Tracking app (implicit).** Look under Themes → Customize → App embeds for a switched-on tracking embed, and under Customer events for its app pixel.

## When hits go to your own subdomain (server-side tracking)

Some stores send GA4 hits to a tagging server on their own subdomain instead of `google-analytics.com`. The server then forwards them to GA4. These servers typically answer on GA4's usual paths such as `/g/collect` ([Google](https://developers.google.com/tag-platform/learn/sst-fundamentals/5-sst-setup-analytics?authuser=4)), so the hit looks like a normal GA4 request on a different host. Managed services do this for you. Leaf Signal, for example, is a fully managed server-side conversion tracking service from the agency Leaf ([BusinessCloud](https://businesscloud.co.uk/?p=190008)).

**Example: a fashion store on a managed tracking service.** No GTM or `gtag` snippet sends GA4 from the theme. Tag Assistant still shows a `view_size_guide` event for destination `G-1A2B3C4D5E`, sent to `https://signal.example-store.com/g/collect` with `gcs=G111` (consent granted). That host is a first-party endpoint run by the store's tracking provider, not Google. Since a pixel can't see button clicks, `view_size_guide` is most likely sent by the provider's theme-side script or a custom event the theme publishes. Only the provider can confirm which.

How to debug this kind of setup:

* **Filter by path, not by host.** In Network, filter `collect?v=2` or `/g/collect`. Filtering by `google-analytics.com` shows nothing and makes GA look broken.
* **A 2xx from your subdomain proves less.** It only means the tagging server received the hit. The server can still drop, rename or fail to forward it, so confirm the event in GA4 DebugView or Realtime.
* **The server side belongs to the provider.** Server-container preview and logs live with whoever runs it (here, the tracking provider). Ask them for access or a test session when a browser hit looks right but GA4 disagrees.
* **Don't add another GA4 tag** because you "can't see Google hits". That creates duplicates (Use case 4).
* **Tool support:** Tag Assistant decoded the custom-domain hit in the example above and named its GA4 destination. Omnibug added support for first-party Google Analytics implementations in 2020 ([Softpedia changelog](https://www.softpedia.com/progChangelog/Omnibug-for-Chrome-Changelog-257741.html)); if a custom-domain hit doesn't appear there, rely on the Network tab.

### One-minute classification test

Paste this into the console on any storefront page:

```javascript
(() => {
  const srcs = [...document.scripts].map(s => s.src);
  console.log('gtag.js scripts :', srcs.filter(s => s.includes('/gtag/js')));
  console.log('gtm.js scripts  :', srcs.filter(s => s.includes('/gtm.js')));
  console.log('Live tag IDs    :', Object.keys(window.google_tag_manager || {}));
  console.log('Pixel sandboxes :', [...document.querySelectorAll('iframe')]
    .map(f => f.name || f.id).filter(n => n.startsWith('web-pixel-sandbox')));
})();
```

This lists what's on the page but can't tell whether gtag.js was hardcoded or injected by GTM, because GTM adds its script to the page too. Use the Network tab's Initiator column for that.

| What you find                                                 | Verdict                            |
| ------------------------------------------------------------- | ---------------------------------- |
| `gtag/js` in theme code; Initiator = the page                 | Direct gtag.js (explicit)          |
| `gtm.js` in theme code; `gtag/js` Initiator = `gtm.js`        | GA4 via GTM (explicit)             |
| No Google code in theme; Google & YouTube shows GA4 connected | Google & YouTube app (implicit)    |
| Google code only inside a custom pixel                        | Custom pixel (explicit, sandboxed) |
| Two or more of these send to the same `G-` ID                 | Double tagging (Use case 4)        |

**Example:** a store's theme has `GTM-K7Q2PL`, Live tag IDs shows `["GTM-K7Q2PL", "G-AB12CD34EF"]`, and Network shows `gtag/js?id=G-AB12CD34EF` with Initiator `gtm.js`. GA4 is loaded explicitly via GTM. Google & YouTube also shows GA4 connected to the same property, so GA4 is _also_ loaded implicitly: two sources, which is a duplicate risk.

**Before you continue:** write down which method sends each GA4 event (page\_view, view\_item, add\_to\_cart, begin\_checkout, purchase). Every later check depends on it.

### Sources

* [Trackingplan: Google Tag Manager on Shopify](https://www.trackingplan.com/blog/google-tag-manager-shopify)
* [Lionel Z.: Shopify pixel consent defaults](https://lionelz.com/en/blog/shopify-ga4-meta-capi-sin-app/)
* [Google: Migrate your Google tags with the Google & YouTube app on Shopify](https://support.google.com/tagmanager/answer/15642481)
* [Elevar: Upgrading from checkout.liquid](https://getelevar.com/shopify/how-to-upgrade-from-checkout-liquid-to-checkout-extensibility/)
* [Two Owls: Shopify GA4 server-to-server integration 2026](https://twoowls.io/blogs/shopify-ga4-server-to-server-integration/)
* [PPC Land: Four events added August 17, 2026](https://ppc.land/shopify-ga4-tracking-gains-4-events-august-17-only-purchase-is-server-side/)
* [Shopify Community: sandbox URLs in GA4 page paths](https://community.shopify.com/t/shopify-pixel-customer-events/324726/7)
* [Google (developers.google.com)](https://developers.google.com/tag-platform/learn/sst-fundamentals/5-sst-setup-analytics?authuser=4)
* [BusinessCloud (businesscloud.co.uk)](https://businesscloud.co.uk/?p=190008)
* [Softpedia changelog (softpedia.com)](https://www.softpedia.com/progChangelog/Omnibug-for-Chrome-Changelog-257741.html)



***

Next: Part 2: Tools and Chrome extensions →
