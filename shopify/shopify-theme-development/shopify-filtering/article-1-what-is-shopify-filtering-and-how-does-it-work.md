# Article 1 — What is Shopify Filtering and How Does It Work?

## Why Filtering Exists <a href="#article-1-of-5" id="article-1-of-5"></a>

When a store has tens, hundreds, or thousands of products, browsing becomes a problem. A customer looking for a blue linen shirt in size medium doesn't want to scroll through 300 items — they want to narrow the list down instantly. That's exactly what filtering does.

**Filtering is the mechanism that lets shoppers refine a product list based on specific attributes** — things like colour, size, price, material, category, or any other characteristic that matters to your customers.

From a business perspective, filtering directly impacts conversion rate. The faster a customer finds what they're looking for, the more likely they are to buy. Poor filtering — or no filtering at all — leads to frustration, higher bounce rates, and lost sales. Good filtering creates a smooth, confident path to purchase.



## Where Filtering Lives in a Shopify Store <a href="#where-filtering-lives-in-a-shopify-store" id="where-filtering-lives-in-a-shopify-store"></a>

It's important to understand upfront: **filtering in Shopify only works on collection pages**.

A collection page is a grouped listing of products — for example, "Men's Shirts", "New Arrivals", or "Sale". When a customer lands on one of these pages, they see a grid or list of products. Filters appear alongside that grid — typically in a sidebar on desktop or a slide-out drawer on mobile — and let the customer refine what they see without navigating away from the page.

Filtering does **not** apply to:

* The homepage (unless it embeds a collection)
* Individual product pages
* Search results pages (search has its own separate filtering logic, also managed through the Search & Discovery app)



## The Core Principle: Filters Are Powered by Your Product Data <a href="#the-core-principle-filters-are-powered-by-your-product-data" id="the-core-principle-filters-are-powered-by-your-product-data"></a>

This is the single most important concept in all of Shopify filtering, and it's worth stating clearly:

> **Shopify does not invent filter options. It reads data that already exists on your products and surfaces that data as filterable choices.**

If your products have a "Colour" option with values like Blue, Red, and Green — Shopify can offer a Colour filter with those three options. If that data doesn't exist on your products, there is nothing to filter by.

This means that **the quality of your filtering is directly tied to the quality and consistency of your product data**. A filter is only as good as the data behind it. This is why merchants who invest time in structured, clean product data get dramatically better filtering results than those who don't.



## How the Filtering Flow Works, Step by Step <a href="#how-the-filtering-flow-works-step-by-step" id="how-the-filtering-flow-works-step-by-step"></a>

Here is exactly what happens when a customer uses a filter on your storefront:

**Step 1 — Customer visits a collection page** Shopify loads all products assigned to that collection and displays them in the default sort order.

**Step 2 — Filter options are rendered** The theme reads the available filter groups (configured in the Search & Discovery app) and displays them as interactive UI elements — checkboxes, sliders, swatches, dropdowns, etc.

**Step 3 — Customer selects a filter value** For example, they tick "Blue" under the Colour filter.

**Step 4 — The URL updates** Shopify appends a filter parameter to the page URL. For example:

```
/collections/shirts?filter.p.option.color=Blue
```

This URL is the machine-readable representation of the customer's filter selection. Each filter type has its own URL parameter format, which we'll cover in detail in a later article.

**Step 5 — Shopify re-queries the product list** Using the Storefront API, Shopify fetches only the products in that collection that match the selected filter value.

**Step 6 — The page updates** The product grid re-renders to show only the matching products — usually without a full page reload, thanks to JavaScript handling the update dynamically.

**Step 7 — Customer refines further or clears filters** They can stack multiple filters (e.g. Blue + Size M + Under £100), and each additional selection further narrows the results. Clearing a filter removes its URL parameter and expands the results again.



## Active Filters vs. Available Filters <a href="#active-filters-vs-available-filters" id="active-filters-vs-available-filters"></a>

Two terms you'll encounter constantly:

**Available filters** — the full set of filter groups and values that _could_ be shown, based on what's configured in Search & Discovery. For example, Colour, Size, and Category might all be available filters.

**Active filters** — the specific values the customer has currently selected. If they've chosen "Blue" and "Size M", those two are the active filters. Themes typically display active filters as removable tags near the top of the product grid so customers can see and clear their selections easily.



## The Role of the Search & Discovery App <a href="#the-role-of-the-search--discovery-app" id="the-role-of-the-search--discovery-app"></a>

Shopify manages storefront filtering through a first-party app called **Search & Discovery**. This app is the control panel between your product data and what actually appears as filters on your storefront.

Think of it this way:

```
Your Product Data  →  Search & Discovery  →  Storefront Filters
(the raw material)     (the configuration)     (what customers see)
```

In Search & Discovery, you decide:

* **Which data** becomes a filter (product options, tags, or metafields)
* **What label** each filter displays under (e.g. rename a technical metafield key to a friendly name like "Material")
* **What order** the filter groups appear in
* **Whether** a filter is active or hidden

The app does not create or modify your product data — it only determines how that data is exposed as filters. The data itself must already exist on your products.



## Filtering vs. Sorting — What's the Difference? <a href="#filtering-vs-sorting--whats-the-difference" id="filtering-vs-sorting--whats-the-difference"></a>

These two are often confused because they appear together on collection pages.

**Filtering** narrows the _set_ of products shown. If you filter by Blue, only blue products appear. Non-blue products are hidden entirely.

**Sorting** changes the _order_ of the products shown, but doesn't remove any. Sorting by "Price: Low to High" reorders all products in the collection — it doesn't hide any of them.

Customers typically filter first (to find the right type of product) and then sort (to find the best option within that type). Both are configured separately — sorting options are also managed in the Search & Discovery app, but they're a distinct feature from filters.



## A Note on Search Filtering <a href="#a-note-on-search-filtering" id="a-note-on-search-filtering"></a>

When a customer uses the search bar on your storefront and gets results, those results can also be filtered. This works similarly to collection filtering but applies to search result pages instead. The same filter groups you configure in Search & Discovery can be made available on search results pages too. We won't go deep on search filtering in this series — the focus here is collection-based filtering — but it's worth knowing the same underlying system powers both.



## Summary <a href="#summary" id="summary"></a>

* Filtering lets customers narrow a product list by specific attributes
* It only works on collection pages (and optionally search results)
* Filters are powered entirely by data that already exists on your products
* The filtering flow works by appending URL parameters and re-querying the product list
* The Search & Discovery app is the configuration layer between your data and your storefront filters
* Filtering (narrowing results) is distinct from sorting (reordering results)
