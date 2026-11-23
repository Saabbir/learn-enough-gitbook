# Google Wants Your Shopify Tags in the Google & YouTube App: What That Means for GA4 and GTM

Google's Tag Manager help center has an article that every Shopify merchant running Google tags should read: [Migrate your Google tags with the Google & YouTube app on Shopify](https://support.google.com/tagmanager/answer/15642481). Its message is simple. On Shopify, Google wants GA4, Google Ads, Floodlight and Merchant Center measurement to run through its own Google & YouTube app, and it is explicit that most other setups are either breaking or unsupported.

This post pulls out what matters from that article, sorts it by the setup you have today, and turns it into a checklist. Everything factual here comes from Google's article; the "what this means" notes are my own reading of it.



## The short version

* **Both deadlines have passed.** Google gives August 28, 2025 for Shopify Plus stores and August 26, 2026 for all other plans. Stores that hadn't migrated by then face serious restrictions from Shopify, which Google warns will hurt reach and campaign and bidding performance.
* **Legacy checkout tags break.** Tags in `checkout.liquid` or in Order status page additional scripts don't work with the new Thank you and Order status pages. Google's advice is to move them to the app _before_ upgrading those pages.
* **Google tags in a custom pixel are unsupported.** Google calls this "not a supported implementation". Data may still arrive, but core features can fail, and Google Support won't help fix it.
* **GTM can't be set up through the app.** Google tells you to move Google tags out of GTM containers on Shopify and configure them in the app instead.
* **The old Online Store → Preferences GA field is gone.** If its tag wasn't moved to the app by February 2, 2025, Shopify turned it into a custom pixel, with the same risks as above.
* **Verify with Tag Assistant** after migrating, and remove duplicate tags.



## Why Google is pushing the app

Shopify's new checkout and its upgraded Thank you and Order status pages changed where code is allowed to run. `checkout.liquid` and Order status additional scripts are being retired, so tags that lived there have nowhere to go. Google's answer is its own app, which it says keeps measurement working with Shopify's checkout now and in future, and can fix inactive or unverified tags that hold back Smart Bidding and Performance Max.

The app also bundles setup for Customer Match and enhanced conversions, and it can create Google tags and conversion events even when it can't connect to an existing account.

**What this means:** Google is optimizing for its ads products. A clean, verified purchase conversion feeds bidding, and the app is the one setup Google will vouch for on checkout pages.



## Does this apply to you? Find your setup

| Your current setup                                                                          | Google's position                                                          | What to do                                                                            |
| ------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| Google tags in `checkout.liquid` or Order status additional scripts                         | Breaks when you upgrade the post-checkout pages                            | Move them to the app first, then upgrade, then remove the old tags                    |
| Google tags (GA4, Ads) loaded in a **custom pixel**, including GTM loaded in a custom pixel | Not supported; may cause data discrepancies and features may stop working  | Move them to the app and remove the pixel's Google tags                               |
| GA4 added through the old **Online Store → Preferences** field                              | Deprecated; converted to a custom pixel if not migrated by Feb 2, 2025     | Migrate the GA4 connection to the app                                                 |
| Google tags inside a **GTM container** on Shopify                                           | GTM can't be set up through the app                                        | Move the Google tags out of GTM and configure them in the app                         |
| A **third-party app or tag manager** (non-Google plugin, server-side service)               | Google can't guarantee tags it didn't deploy, especially on checkout pages | Confirm behavior with your provider; consider adding measurement from the app as well |
| Already on the Google & YouTube app                                                         | Recommended setup                                                          | Check for duplicate tags elsewhere and verify with Tag Assistant                      |

**What this means for a common setup:** a custom pixel that loads GTM only on `/checkouts/` pages so GA4 and Google Ads can see purchases is exactly the pattern Google now calls unsupported. It may still send data, but if conversions go inactive or bidding features misbehave, Google Support won't treat it as their problem.



## What the app can't do

Before you migrate, know the limits Google lists:

* **No GTM.** Shopify's platform limits stop GTM from being set up through the app.
* **One account per product.** You can link one Google Ads, one GA4 and one Merchant Center account. Extra accounts need their Google tags added manually.
* **Custom code isn't copied.** Settings you added in code, such as privacy or feature settings, aren't carried over automatically. You'll need to handle them separately.
* **Admin access is required to connect.** The Google account linked in the app must be a direct admin of the Google Ads, GA4 or Merchant Center account. If you can't get that, Google says you can still migrate the tags without connecting, so measurement doesn't break.



## Migration checklist

1. **Inventory every Google tag on the store.** Theme code, GTM containers, custom pixels, Additional scripts and any tracking apps. Part 1 of the series shows how to find them all.
2. **Check access.** Make sure the person connecting the app is a direct admin of each Google account. If an agency manages the accounts, migrate together with them.
3. **Pause third-party product sync conflicts.** If another tool syncs products to Merchant Center, turn off the app's product sync during onboarding.
4. **Install or open the Google & YouTube app.** If it's already installed, look for the banner on its Overview page that offers to migrate the tags it detected, and click **Get started**. The app detects tags affected by the post-checkout upgrade across Google Ads, GA4, Floodlight and Merchant Center.
5. **Fix domain mismatches (multiple domains only).** The app claims a website automatically, which may not match your product URLs. In Merchant Center, open **Settings → Business info → Edit online store**, enter the domain your product URLs use (it must be listed under Shopify **Settings → Domains**), then **Save**, **Change**, and claim the store if asked.
6. **Remove duplicates.** Delete the old Google tags from `checkout.liquid`, Additional scripts, custom pixels and GTM once the app is live. Two sources for one conversion means double counting. Google has a guide to [removing duplicate tags](https://support.google.com/tagmanager/answer/16424072).
7. **Verify.** Use [Google Tag Assistant](https://support.google.com/google-ads/answer/10989978) to confirm conversion tags fire, then follow Google's [Verify your upgrade](https://support.google.com/tagmanager/answer/16138144) guide.



## Gotchas worth knowing

* **Purchase values may look lower, and that's a correction.** In late April 2025 Shopify fixed how order-level discounts appear on the new Thank you page: `subtotalPrice` now reflects the discounted subtotal. From around April 22, 2025, stores that use `subtotalPrice` for conversion value may see lower purchase values. Check this before assuming tracking broke.
* **Agencies and multiple accounts.** If you manage several Google Ads or GA4 accounts, only one of each can be linked; the rest stay manual.
* **Third-party and server-side tracking.** Google doesn't say these are banned, only that it can't vouch for them on checkout pages. If you use a managed or server-side service, ask the provider how it coexists with the app, and make sure the two aren't both sending the same purchase.



## My take

If GA4 and Google Ads are your main tools, follow Google's advice: move Google tags into the app, remove the copies in custom pixels and GTM, and keep GTM (if at all) for tags Google's article doesn't cover. Google's article is about Google tags only; it doesn't say what to do with non-Google tags in GTM.

If you depend on things the app can't do, such as several GA4 properties, custom consent logic in code, or a server-side setup, you can keep a custom setup. Just go in knowing it's unsupported by Google, document it, and verify it after every Shopify checkout change.



## Source

* [Google Tag Manager Help: Migrate your Google tags with the Google & YouTube app on Shopify](https://support.google.com/tagmanager/answer/15642481)
