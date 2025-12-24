# Shopify’s Data Model: Core Objects & How Shopify Models Ecommerce

Understanding **Shopify’s data model** is essential for building reliable themes, apps, integrations, and custom storefronts. Shopify abstracts complex ecommerce operations into a well-defined set of objects that mirror how real-world commerce works—products are browsed, added to carts, paid for, and fulfilled.

This article explains:

* How Shopify models ecommerce at a high level
* How a typical customer journey maps to data
* The most important Shopify objects and how they connect
* Where niche objects fit (B2B, subscriptions, fulfillment)

## 🧠 How Shopify Models Ecommerce

At its core, Shopify is built around **four primary ecommerce concepts**:

* **Products**
* **Collections**
* **Customers**
* **Orders**

These objects form the backbone of nearly every Shopify store. Whether you’re working on the storefront (Liquid, Hydrogen) or backend (Admin API, webhooks), you’ll constantly interact with them.

> **Key idea:** Shopify’s data model reflects _how commerce actually happens_, not just how data is stored.

## 🔁 How Shopify Models the Ecommerce Experience

A standard ecommerce journey in Shopify looks like this:

1. A customer browses products on a sales channel
2. The customer adds items to a cart
3. The customer initiates checkout
4. The customer pays, creating an order
5. The brand fulfills the order

Let’s map each step to Shopify’s data objects.

## 1️⃣ Browsing Products on a Channel

A **sales channel** is any surface where products can be discovered and purchased:

* Online Store (theme or headless)
* Point of Sale (POS)
* Shop App
* Marketplaces or social channels

#### Products & Variants

* A **product** represents what is sold.
* Every product has **at least one variant** (the default).
* Variants represent purchasable combinations of options (e.g., size, color).

Each variant has its own:

* Price
* SKU
* Inventory
* Availability per location

#### Collections

**Collections** group products to help with:

* Navigation
* Merchandising
* Discounts
* Automated catalog management

Collections don’t sell anything directly—they _organize_ products for customers and admins.

## 2️⃣ Adding Items to the Cart

When a customer adds items, Shopify creates a **cart**.

#### Important Cart Characteristics

* Carts are **channel-specific**
* A cart contains **line items**
* Each line item references:
  * A **product variant**
  * A **quantity**

At this stage:

* Prices are shown as a **subtotal**
* Taxes, shipping, and discounts may be estimated
* **No order exists yet**

> A cart is **temporary state**—it only becomes permanent after payment.

## 3️⃣ Initiating Checkout

Checkout begins when the customer proceeds from the cart.

#### Checkout Responsibilities

* Collect customer information
* Capture shipping address
* Present delivery options
* Apply discount codes or automatic discounts
* Calculate taxes and shipping

Shopify’s checkout typically produces:

* Checkout page(s)
* **Thank You page**
* **Order Status page**

These pages are generated _after_ successful payment.

## 4️⃣ Payment Creates an Order

Once payment is successful, Shopify creates an **order**.

#### Orders Are Central Objects

An order links:

* Customer
* Line items (variants)
* Payment details
* Taxes
* Discounts
* Shipping method
* Fulfillment requirements

The **Thank You page** includes:

* Order number
* Summary of items
* Contact and delivery info

The **Order Status page** allows customers to:

* Track fulfillment
* View shipment updates
* Revisit order details

> **Key insight:** Payment is the event that transforms a cart into an order.

## 5️⃣ Fulfilling the Order

After an order is created, it must be fulfilled.

#### Fulfillment in Shopify

* Inventory is adjusted at the **location** level
* Orders may be split into **fulfillment orders**
* Each fulfillment order represents a shipment task

Brands can fulfill:

* In-house
* Via third-party logistics (3PL)
* Via dropshipping or fulfillment apps

Tracking details are shared with the customer once shipped.

## 🧩 Core Ecommerce Objects in Shopify

Below are the most important objects you’ll work with as a developer and how they fit together.

#### Products

Represents items for sale.\
Connected to variants, inventory, collections, selling plans, discounts, and markets.

#### Collections

Logical groupings of products.\
Used for navigation, merchandising, and automation.

#### Customers

Stores buyer identity and history.\
Connected to orders, addresses, segments, and (in B2B) companies.

#### Companies (B2B)

Represents business entities for B2B commerce.\
Links multiple customers under a single company account.

#### Locations

Physical or virtual places where inventory is stored or fulfilled from.

#### Orders

Permanent records of transactions.\
Connected to customers, payments, fulfillment orders, discounts, and shipping.

#### Discounts

Rules that modify pricing.\
Can apply to products, collections, orders, or shipping.

#### Markets

Defines selling contexts (regions, currencies, languages, pricing rules).

#### Fulfillment Orders

Breaks an order into fulfillable units per location or service.

#### Fulfillment Services

Represents third-party logistics or fulfillment providers.

#### Gift Cards

Tracks store credit issuance and redemption.

#### Selling Plans

Defines alternate purchase options such as:

* Subscriptions
* Pre-orders
* Installments

## 🧩 Shopify Core Data Objects (Reference Table)

| **Object**               | **Description**                                                                                       | **Connected resources**                                                     |
| ------------------------ | ----------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **Products**             | Represents individual items available for sale in the store. Products define what a business sells.   | Variants, Collections, Inventory Items, Locations, Selling Plans, Discounts |
| **Variants**             | Specific purchasable versions of a product based on options (size, color, etc.).                      | Products, Inventory Items, Prices, Line Items                               |
| **Collections**          | Groups products together for browsing, merchandising, and automation.                                 | Products, Discounts                                                         |
| **Customers**            | Represents people who place orders or create accounts in the store.                                   | Orders, Addresses, Gift Cards, Companies                                    |
| **Companies**            | Represents business entities used for B2B commerce.                                                   | Customers, Company Locations, Orders                                        |
| **Orders**               | A completed transaction created after successful checkout and payment.                                | Customers, Line Items, Payments, Fulfillment Orders, Discounts              |
| **Carts**                | Temporary container holding items before checkout is completed.                                       | Line Items, Product Variants                                                |
| **Line Items**           | Individual entries inside a cart or order, each referencing a product variant and quantity.           | Products, Variants, Orders                                                  |
| **Locations**            | Physical or virtual places where inventory is stocked or orders are fulfilled.                        | Inventory Levels, Fulfillment Orders                                        |
| **Inventory Items**      | Tracks stock availability for a specific variant across locations.                                    | Variants, Locations                                                         |
| **Fulfillment Orders**   | Represents tasks required to fulfill parts of an order from specific locations or services.           | Orders, Locations, Fulfillment Services                                     |
| **Fulfillment Services** | Third-party services or apps responsible for shipping and handling fulfillment.                       | Fulfillment Orders                                                          |
| **Discounts**            | Defines promotional rules that modify pricing or shipping.                                            | Products, Collections, Orders                                               |
| **Markets**              | Defines geographic or contextual selling regions with specific pricing, currency, and language rules. | Products, Prices, Taxes                                                     |
| **Selling Plans**        | Defines alternate purchase options like subscriptions, pre-orders, or installments.                   | Products, Variants                                                          |
| **Gift Cards**           | Represents store credit that can be issued, redeemed, and tracked.                                    | Customers, Orders                                                           |

## 🧠 How These Objects Work Together (Mental Model)

Think of Shopify’s data model as a connected graph:

* **Products** → grouped by **Collections**
* **Variants** → added to **Carts**
* **Carts** → converted into **Orders**
* **Orders** → fulfilled via **Locations** and **Fulfillment Orders**
* **Customers** → linked to **Orders**
* **Discounts & Markets** → modify pricing and availability
* **Selling Plans** → change how products are purchased

Understanding these relationships makes theme logic, API usage, and integrations far more predictable.

## 🎯 Niche Objects & Advanced Use Cases (Especially B2B)

Not every store uses every object.

#### B2B-Specific Objects

When building B2B solutions, you’ll often use:

* `customer.b2b?`
* `customer.company_available_locations`
* `customer.current_company`
* `customer.current_location`

These objects allow:

* Company-based pricing
* Purchase permissions
* Location-specific catalogs
* Approval workflows

B2B stores model _organizational buying_, not just individual consumers.

## ✅ Summary (Key Takeaways)

* Shopify models ecommerce around real-world workflows
* Products, collections, customers, and orders are the core objects
* Carts are temporary; orders are permanent
* Fulfillment is location- and service-driven
* Markets and selling plans modify how products are sold
* B2B introduces company-based relationships
* Knowing object relationships is essential for quality development

## 📚 Further Reading

* Shopify Dev Docs: Products & Variants
* Shopify Dev Docs: Orders & Fulfillment
* Shopify Dev Docs: Customers & Segments
* Shopify Dev Docs: Markets & Internationalization
* Shopify Dev Docs: B2B on Shopify
* Shopify Dev Docs: Selling Plans & Subscriptions

## ❓ FAQs / Quick Quiz

**Q1: Does a cart exist after checkout is completed?**\
No. A cart becomes an order only after payment.

**Q2: Can a product exist without variants?**\
No. Every product has at least one variant.

**Q3: Are collections purchasable objects?**\
No. Collections only group products.

**Q4: What object represents a shipment task?**\
Fulfillment Order.

**Q5: Which object enables subscriptions or pre-orders?**\
Selling Plan.
