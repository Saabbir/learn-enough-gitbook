---
icon: '2'
---

# Tools and Chrome Extensions for Debugging GA4 on Shopify

{% hint style="info" %}
_Part 2 of 6 in the Debugging GA4 on Shopify series._
{% endhint %}

Once you know how GA4 is loaded, you need the right tools to watch it work. Shopify makes this harder than on most sites, because a lot of tracking now runs inside a sandbox that many extensions can't see into. This part compares the browser tools and Chrome extensions worth having, including Omnibug, and what each one can and can't show you on a Shopify store.

> **New to this series?** GA4 on Shopify can run in two places: on the page itself (theme code) or inside Shopify's pixel sandbox (custom pixels and app pixels). It is also either _explicit_ (code your team wrote and can edit) or _implicit_ (managed by Shopify, Google or an app). Part 1 shows how to tell which setup you have.



## The tools at a glance

The Network tab is the single source of truth for browser tracking: if no `collect` request leaves the browser, GA4 got nothing from it. Extensions like Omnibug show those same requests in readable form, and each other tool answers one narrower question.

| Tool                                                                                              | What it shows                                                                                                       | Shopify catch                                                                                                                                                                                                                                                                                                  |
| ------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Chrome DevTools → **Network** (filter `collect?v=2`)                                              | Every GA4 hit: Measurement ID (`tid`), event name (`en`), parameters, and which script sent it (Initiator)          | Tick **Preserve log** so the redirect into checkout doesn't wipe it                                                                                                                                                                                                                                            |
| Chrome DevTools → **Console**                                                                     | `gtag`, `dataLayer`, `google_tag_manager`, runtime errors                                                           | A custom pixel runs in a sandboxed iframe: pick it in the console's context selector. Shopify doesn't name the frame, so you may have to look through the options ([Shopify](https://help.shopify.com/en/manual/promoting-marketing/pixels/custom-pixels/testing)); their names start with `web-pixel-sandbox` |
| **Omnibug** (Chrome, Firefox, Edge)                                                               | Decodes outgoing marketing requests, GA4 included, into a readable table inside DevTools; filter by account, export | Only sees what the browser sends, so a server-side purchase won't appear                                                                                                                                                                                                                                       |
| **Shopify Pixel Helper**                                                                          | Which pixels loaded, which Shopify events each one received, errors, "awaiting consent"                             | Proves delivery to the sandbox, not to GA4; doesn't work on headless stores or stores in private mode ([Shopify](https://help.shopify.com/en/manual/promoting-marketing/pixels/custom-pixels/testing))                                                                                                         |
| **Google Tag Assistant** (extension + [tagassistant.google.com](https://tagassistant.google.com)) | Google tags on the page, events, consent state; runs GTM Preview                                                    | May not detect tags loaded through a custom pixel ([Stape](https://stape.io/blog/shopify-google-analytics-4-tracking))                                                                                                                                                                                         |
| **Analytics Debugger** (David Vallejo)                                                            | DevTools panel for debugging analytics hits, GA4 included                                                           | Same as Omnibug: browser hits only                                                                                                                                                                                                                                                                             |
| **Adswerve dataLayer Inspector+**                                                                 | `dataLayer` pushes as they happen                                                                                   | Shows the top page's dataLayer, not one inside a pixel sandbox                                                                                                                                                                                                                                                 |
| **Google Analytics UTILS**                                                                        | Switches on GA4 debug mode, blocks GA/GTM, swaps the GA4 ID                                                         | Injection-based tools can't reach code inside the pixel sandbox ([WeltPixel](https://weltpixel.com/blogs/news/ga4-debugview-shopify-verify-events-firing))                                                                                                                                                     |
| GA4 → Admin → **DebugView**                                                                       | Each event from your device, with parameters and items                                                              | Only events flagged `debug_mode` (Use case 2)                                                                                                                                                                                                                                                                  |
| GA4 → Reports → **Realtime**                                                                      | Events from the last 30 minutes                                                                                     | The only browser-free check for server-side purchases                                                                                                                                                                                                                                                          |

Note on Tag Assistant: Google is retiring the separate **Tag Assistant Companion** extension and has moved its features into the main Tag Assistant extension ([Chrome Web Store](https://chromewebstore.google.com/detail/tag-assistant-companion/jmekfmbnaedfebfnmakmokmlfpblbfdm?hl=en-GB)). Install Tag Assistant, not the Companion.



## Which tool answers which question

* Did a hit leave the browser? → **Network tab** or **Omnibug**
* Did Shopify deliver the event to my pixel? → **Shopify Pixel Helper**
* Is my GTM container or Google tag set up right? → **Tag Assistant** (GTM Preview)
* Did GA4 actually receive it? → **DebugView** or **Realtime**



## Using Omnibug on a Shopify store

1. Install Omnibug, open your storefront, then open DevTools (F12) and choose the **Omnibug** tab. It lives in DevTools, not in a toolbar popup.
2. In Omnibug's settings, keep only the **Google Analytics 4** provider (and **Google Tag Manager** if you use it) so other pixels don't clutter the list.
3. Reload the page. Each row is one request: check the **Measurement ID**, **Event** (`page_view`, `view_item`, etc.) and the parameter list underneath.
4. Do one action at a time (open a product, add to cart, start checkout) and match each new row to that action.
5. If a hit appears in the Network tab but not in Omnibug, trust the Network tab.

**Example:** on a product page Omnibug shows two GA4 rows: `page_view` and `view_item` for `G-AB12CD34EF`, with `currency=USD`, `value=29` and one item. Tracking for that page is working. If it shows a third row, `page_view` again for the same ID, you have duplicates (Use case 4).



## Sources

* [Shopify Help Center: Testing custom pixels](https://help.shopify.com/en/manual/promoting-marketing/pixels/custom-pixels/testing)
* [tagassistant.google.com (tagassistant.google.com)](https://tagassistant.google.com)
* [Stape: Shopify GA4 tracking](https://stape.io/blog/shopify-google-analytics-4-tracking)
* [WeltPixel: GA4 DebugView on Shopify](https://weltpixel.com/blogs/news/ga4-debugview-shopify-verify-events-firing)
* [Tag Assistant Companion retirement notice](https://chromewebstore.google.com/detail/tag-assistant-companion/jmekfmbnaedfebfnmakmokmlfpblbfdm?hl=en-GB)
* [Omnibug (Chrome Web Store)](https://chromewebstore.google.com/detail/omnibug/bknpehncffejahipecakbfkomebjmokl)



***

Next: Part 3: Is GA4 loaded and are events firing? →
