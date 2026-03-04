# 📘 ARTICLE 05 — Understanding Google Tag IDs and Script Types

In the previous articles, we learned:

* how website tracking works
* how Google Analytics works
* how Google Tag Manager manages tags

When auditing a website, one of the first skills you must develop is the ability to **recognize tracking scripts quickly**.

Most tracking scripts contain **identifiable ID patterns**.

For example:

```
G-XXXXXXXXXX
GTM-XXXXXXX
AW-XXXXXXXX
DC-XXXXXXXX
```

By learning these patterns, you can immediately determine:

* what tool is installed
* what type of tracking is running
* whether there are duplicate implementations

This article explains **all major Google tag identifiers and scripts**.



## 1. The Google Tag Ecosystem

Google offers several tracking products.

Each product uses its own tag type.

The most common Google tags are:

| Tag Type           | Purpose                           |
| ------------------ | --------------------------------- |
| Google Analytics   | website analytics                 |
| Google Tag Manager | tag management                    |
| Google Ads         | ad conversion tracking            |
| Floodlight         | DV360 advertising tracking        |
| Conversion Linker  | preserves ad attribution          |
| Google Tag         | unified Google tracking framework |

Understanding these tags is critical for **analytics auditing**.



## 2. Google Analytics Measurement ID

GA4 properties use a **Measurement ID**.

Format:

```
G-XXXXXXXXXX
```

Example:

```
G-9V5CXZEBCN
```

This ID tells the analytics script **where to send event data**.

Example GA4 configuration:

```
gtag('config', 'G-XXXXXXXXXX');
```

This initializes Google Analytics tracking.



## 3. Google Tag Manager Container ID

Google Tag Manager containers use the prefix:

```
GTM-XXXXXXX
```

Example:

```
GTM-KWBP87
```

The GTM script loads the container.

Example:

```
https://www.googletagmanager.com/gtm.js?id=GTM-KWBP87
```

Once loaded, GTM controls all other tags.



## 4. Google Ads Conversion ID

Google Ads uses a conversion ID.

Format:

```
AW-XXXXXXXX
```

Example:

```
AW-123456789
```

This ID tracks advertising conversions.

Example conversion event:

```
gtag('event', 'conversion', {
  'send_to': 'AW-123456789/abc123'
});
```

This tells Google Ads that a conversion occurred.



## 5. Floodlight Tag (Display & Video 360)

Floodlight tags are used in **enterprise advertising platforms** like Display & Video 360.

Floodlight ID format:

```
DC-XXXXXXXX
```

These tags track:

```
impressions
conversions
remarketing audiences
```

Floodlight tags are less common on Shopify stores but may appear on large enterprise sites.



## 6. Google Tag (Unified Tag System)

Google introduced a newer architecture called the **Google Tag**.

Format:

```
GT-XXXXXXXX
```

The Google tag allows one script to power multiple products:

```
Google Analytics
Google Ads
Floodlight
```

Example script:

```
gtag('config', 'GT-XXXXXXXX');
```

This approach simplifies tag management.



## 7. The gtag.js Script

The `gtag.js` library is the core JavaScript used by many Google tags.

Example script:

```
https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX
```

This script loads the Google tag framework.

Once loaded, it can send events to:

```
GA4
Google Ads
Floodlight
```

Example event:

```
gtag('event', 'purchase', {
  value: 100,
  currency: 'USD'
});
```



## 8. analytics.js (Legacy Script)

Older Google Analytics implementations used the `analytics.js` script.

Example:

```
https://www.google-analytics.com/analytics.js
```

This script supported **Universal Analytics**.

Example tracking code:

```
ga('create', 'UA-12345678-1', 'auto');
```

If you see `analytics.js` during an audit, it usually means:

```
legacy tracking still exists
```



## 9. gtm.js Script

The `gtm.js` script loads Google Tag Manager containers.

Example:

```
https://www.googletagmanager.com/gtm.js?id=GTM-XXXX
```

This script loads all tags configured inside the GTM container.

Important distinction:

```
gtm.js → loads GTM
gtag.js → loads Google tag framework
```



## 10. How to Recognize Tags During an Audit

When inspecting a website, you should search for the following patterns.

### Page Source Search

Open page source and search for:

```
GTM-
G-
AW-
gtag
analytics.js
gtm.js
```

These patterns reveal installed tags.

### Browser DevTools Network Tab

You can also check network requests.

Filter requests by:

```
gtm.js
gtag/js
collect
```

Example request:

```
https://www.google-analytics.com/g/collect
```

This indicates GA4 events being sent.

### Chrome Tag Assistant

Tag Assistant detects:

```
Google Analytics
Google Ads
Google Tag Manager
```

This tool helps confirm which tags are installed.



## 11. Common Tag Combinations on Shopify

Many Shopify stores use the following combination.

Example architecture:

```
Shopify store
↓
Google Tag Manager
↓
GA4
Google Ads
Meta Pixel
TikTok Pixel
```

However, many stores also use Shopify apps.

Example:

```
Google & YouTube Shopify App
```

This app may automatically install:

```
GA4
Google Ads conversions
```

This often leads to **duplicate tracking**.



## 12. Duplicate Tagging Example

Common duplication scenario:

```
GA4 installed via GTM
+
GA4 installed via Shopify app
```

Result:

```
duplicate pageview events
duplicate purchase events
inflated analytics data
```

This is one of the most common issues found during Shopify analytics audits.



## 13. Quick Tag Identification Guide

| Tag Pattern | Meaning                           |
| ----------- | --------------------------------- |
| GTM-XXXX    | Google Tag Manager container      |
| G-XXXX      | Google Analytics 4 measurement ID |
| AW-XXXX     | Google Ads conversion ID          |
| DC-XXXX     | Floodlight tracking ID            |
| GT-XXXX     | Unified Google Tag                |

Learning these patterns allows you to quickly **identify tracking systems on any website**.



## Key Takeaways

Important concepts from this article:

* Google tracking tools use identifiable ID formats.
* GA4 uses **G-XXXXXXXXXX** measurement IDs.
* GTM containers use **GTM-XXXXXXX** IDs.
* Google Ads uses **AW-XXXXXXXX** IDs.
* Floodlight uses **DC-XXXXXXXX** IDs.
* Recognizing these IDs helps detect tracking implementations during audits.



## Next Article

### 📘 ARTICLE 06 — How Tracking Is Implemented on Shopify

In the next article we will explore:

* where tracking scripts live inside Shopify themes
* Shopify app-based tracking
* Shopify Web Pixels
* checkout tracking limitations
* common Shopify tracking architectures

This knowledge is essential before performing **Shopify analytics audits**.
