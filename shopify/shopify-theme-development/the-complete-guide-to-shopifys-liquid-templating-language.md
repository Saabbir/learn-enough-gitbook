---
description: >-
  A deep dive into syntax, objects, tags, filters, operators, and everything you
  need to master Shopify theme development.
---

# The Complete Guide to Shopify’s Liquid Templating Language

## **Table of Contents**

1. What Is Liquid?
2. Core Concepts: Objects, Tags & Filters
3. Assigning Variables
4. Output & Interpolation
5. Truthiness & Falsiness
6. Whitespace Control
7. Logic & Operators
8. Control Flow Tags
9. Loops & Iteration
10. Key Shopify Global Objects
11. Essential Liquid Filters
12. Image Filters (`img_url`)
13. Comments
14. Rendering Snippets
15. Full Practical Example
16. Final Thoughts

***

## **1. What Is Liquid?**

Liquid is Shopify’s server-side templating language used to:

* Dynamically render objects (products, shop data, cart)
* Build logic (if/else, loops, case statements)
* Transform output with filters
* Assemble HTML for themes

Liquid runs on Shopify’s servers, meaning your theme is rendered into pure HTML **before** reaching the browser.

***

## **2. Core Concepts: Objects, Tags & Filters**

Liquid has three building blocks:

***

### **Objects — Output Data**

Wrapped in `{{ }}`:

```liquid
{{ product.title }}
{{ shop.name }}
{{ cart.item_count }}
```

Objects represent Shopify’s backend data structures.

***

### **Tags — Logic & Control Flow**

Wrapped in `{% %}`:

```liquid
{% assign greeting = "Hello" %}
{% if product.available %}
{% for item in cart.items %}
```

Tags control _logic_, not output.

***

### **Filters — Modify Output**

Applied using the `|` pipe:

```liquid
{{ product.title | upcase }}
{{ price | money }}
{{ string | truncate: 30 }}
```

Filters transform objects before rendering.

***

## **3. Assigning Variables**

Use `assign` to create or modify values:

```liquid
{% assign name = "Liquid" %}
```

Multiple operations:

```liquid
{% assign message = "Hello " | append: name %}
```

Variables exist only inside the current scope.

***

## **4. Output & Interpolation**

Output is handled via `{{ }}`:

```liquid
{{ name }}
{{ name | upcase }}
{{ shop.currency }}
```

Objects → Properties:

```liquid
{{ product }}
{{ product.title }}
```

***

## **5. Truthiness & Falsiness (Important!)**

Liquid has **ONLY two falsy values**:

* `false`
* `nil`

Everything else is truthy:

* `""` empty string → truthy
* `[]` empty array → truthy
* `{}` empty object → truthy
* `0` → truthy
* empty metafield → truthy

#### Correct way to check emptiness:

```liquid
{% if product.tags == blank %}
```

Use `blank` or `empty`, NOT a simple truthiness check.

***

## **6. Whitespace Control**

Liquid tags produce whitespace unless stripped.

Add hyphens:

```liquid
{%- assign name = "Liquid" -%}
```

Example with loop:

```liquid
<ul>
{%- for tag in blog.tags -%}
  <li>{{ tag }}</li>
{%- endfor -%}
</ul>
```

Prevents extra blank lines in rendered HTML.

***

## **7. Logic & Operators**

#### **Comparison operators**

* `==`
* `!=`
* `>`
* `<`
* `>=`
* `<=`

#### **Logical operators**

* `and`
* `or`

Example:

```liquid
{% if product.available and product.price > 1000 %}
  Premium product!
{% endif %}
```

***

## **8. Control Flow Tags**

#### **IF**

```liquid
{% if product.available %}
  In stock
{% elsif product.quantity == 0 %}
  Out of stock
{% else %}
  Coming soon
{% endif %}
```

***

#### **CASE**

```liquid
{% case product.type %}
  {% when "Shoes" %}
  {% when "Accessories" %}
  {% else %}
{% endcase %}
```

***

## **9. Loops & Iteration**

#### Basic loop:

```liquid
{% for product in collection.products %}
```

#### Useful modifiers:

* `limit`
* `offset`
* `reversed`
* `break`
* `continue`

Example:

```liquid
{% for product in collection.products limit: 4 %}
  {{ product.title }}
{% endfor %}
```

***

#### Pagination:

```liquid
{% paginate collection.products by 12 %}
  {% for product in paginate.items %}
  {% endfor %}
{% endpaginate %}
```

***

## **10. Key Shopify Global Objects**

These are the objects you'll use daily.

***

### **Storefront & Theme**

* `shop`
* `settings`
* `routes`
* `localization`
* `linklists`
* `content_for_header`
* `content_for_layout`

***

### **Products**

* `product`
* `product.variants`
* `product.selected_or_first_available_variant`
* `product.options_with_values`
* `product.metafields`

***

### **Collections**

* `collection`
* `collection.products`
* `collection.all_products`

***

### **Cart**

* `cart`
* `cart.items`
* `cart.total_price`
* `cart.item_count`

***

### **Blog & Article**

* `blog`
* `articles`
* `article`

***

### **Section & Block**

Used in Online Store 2.0 sections:

* `section`
* `block`

***

## **11. Essential Liquid Filters (Most Used)**

Grouped for clarity:

***

### **String Filters**

* `upcase`
* `downcase`
* `capitalize`
* `truncate`
* `replace`
* `strip_html`
* `append`
* `prepend`

Example:

```liquid
{{ title | truncate: 30 }}
```

***

### **Array Filters**

* `map`
* `join`
* `uniq`
* `compact`
* `reverse`
* `sort`

Example:

```liquid
{{ collection.products | map: "title" | join: ", " }}
```

***

### **Money Filters**

* `money`
* `money_with_currency`
* `money_without_trailing_zeros`

***

### **Math Filters**

* `plus`
* `minus`
* `times`
* `divided_by`

***

### **Localization Filter**

* `t` (translates a key in locale JSON)

***

### **JSON Filter**

Convert data for JavaScript.

```liquid
{{ cart | json }}
```

***

## **12. Image Filter: `img_url`**

Shopify’s most commonly used image filter.

Example:

```liquid
{{ product.featured_image | img_url: '400x400' }}
```

Common sizes:

* `thumb`
* `small`
* `medium`
* `large`
* `grande`
* numeric sizes: `"600x"`
* constrained: `"800x800"`

Reference: [https://shopify.dev/docs/api/liquid/filters/img\_url#img\_url-size](https://shopify.dev/docs/api/liquid/filters/img_url#img_url-size)

***

## **13. Comments**

```liquid
{% comment %}
  Hidden code
{% endcomment %}
```

***

## **14. Rendering Snippets**

Modern syntax:

```liquid
{% render 'product-card', product: product %}
```

`include` is deprecated.

***

## **15. Full Practical Example**

Putting everything together:

```liquid
{%- assign featured = collections['frontpage'].products -%}

{% if featured == blank %}
  <p>No products found.</p>
{% else %}
  <ul>
  {%- for product in featured limit: 4 -%}
    <li>
      <a href="{{ product.url }}">
        <img 
          src="{{ product.featured_image | img_url: '400x' }}" 
          alt="{{ product.title }}"
        >
        {{ product.title | truncate: 20 }}
        {{ product.price | money }}
      </a>
    </li>
  {%- endfor -%}
  </ul>
{% endif %}
```

This example uses:

* tags
* objects
* filters
* whitespace control
* loops
* conditional logic

A realistic snippet for most Shopify themes.

***

## **16. Final Thoughts**

Mastering Liquid unlocks the full power of Shopify theme development.\
Once you understand:

✔ Objects\
✔ Tags\
✔ Filters\
✔ Truthiness\
✔ Whitespace\
✔ Loops & logic\
✔ Shopify-specific helpers

…you can build:

* custom sections
* advanced product templates
* dynamic cart experiences
* metafield-powered designs
* fully featured OS 2.0 themes
