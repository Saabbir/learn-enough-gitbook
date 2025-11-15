# Understanding Shopify Sections & Blocks: The Heart of Modern Theme Architecture

_If Shopify’s theme architecture is the house… then sections are the rooms, and blocks are the furniture you place inside those rooms._

This article dives deep into Shopify’s most powerful concept: **Sections & Blocks**.\
This is where Online Store 2.0 truly shines — and where your themes become flexible, modular, and merchant-friendly.

Let’s begin at the origin.

***

## **1. The Evolution: Why Sections Became Shopify’s Core Building Block**

In the early days of Shopify (pre–Online Store 2.0):

* Only the **homepage** could use sections
* Every other page had rigid, fixed layouts
* Developers had to hardcode content
* Merchants asked developers for every tiny layout update

This was frustrating for everyone.

Then Shopify introduced **Online Store 2.0** — and everything changed.

Sections could now live **on any page**.\
Templates could now be **JSON**, listing sections dynamically.\
Blocks became flexible units that merchants could add per section.

The result:

#### **A fully modular theme development system.**

One where:

* Developers build components
* Merchants assemble pages
* Apps inject their own blocks
* Pages become infinitely customizable

Let’s break down how all of this works.

***

## **2. What Exactly Is a Shopify Section?**

A section is a **self-contained, customizable component**.\
You can think of it as a “mini-app” inside the theme.

A section can:

* Render HTML + Liquid
* Accept customizable settings
* Contain blocks
* Have presets
* Be added to JSON templates
* Store merchant preferences

Examples of sections:

* Product information
* Slideshow
* Featured product
* Image banner
* Rich text section
* Header footer

Most importantly:

#### **Sections = building blocks of every theme page.**

Now let’s look inside one.

***

## **3. Anatomy of a Section (The Two Main Parts)**

Every section is made of:

### **1. Liquid Markup**

This is the template code:

* HTML
* CSS
* JavaScript
* Liquid logic
* Objects like `product`, `collection`, `settings`, etc.

Example:

```liquid
<div class="section-title">
  <h2>{{ section.settings.title }}</h2>
</div>
```

### **2. JSON Schema**

This is the metadata that configures the section.

It defines:

* Settings
* Blocks
* Presets
* Input fields
* Info shown in the Theme Editor

Every schema is wrapped inside:

```liquid
{% schema %}
{
  ...
}
{% endschema %}
```

Let’s walk through this step by step.

***

## **4. Section Settings (The Customizable Inputs)**

Settings give merchants control over how a section appears.

Example:

```json
"settings": [
  {
    "type": "text",
    "id": "title",
    "label": "Heading",
    "default": "Featured Collection"
  },
  {
    "type": "collection",
    "id": "collection",
    "label": "Choose a collection"
  }
]
```

Supported input types include:

* text
* textarea
* richtext
* image\_picker
* color
* range
* number
* checkbox
* font\_picker
* product / collection / page selectors
* URL

The merchant edits these via the Theme Editor.\
The values are accessed in Liquid like:

```
section.settings.title
section.settings.collection
```

***

## **5. Blocks (Modular Pieces Inside Sections)**

If a Section is a room, then Blocks are the furniture the merchant can add, remove, and rearrange.

Blocks allow dynamic content _inside_ a section.

Block example:

```json
"blocks": [
  {
    "type": "text_block",
    "name": "Text block",
    "settings": [
      { "type": "text", "id": "text", "label": "Text" }
    ]
  }
]
```

Liquid rendering:

```liquid
{% for block in section.blocks %}
  <div>{{ block.settings.text }}</div>
{% endfor %}
```

#### **Each block has:**

* A unique ID
* A type
* Its own settings
* Merchant-editable content
* Drag-and-drop reordering

#### **Block Limits**

* Max 50 per section
* Max 25 sections per template

***

## **6. Static Sections vs Dynamic Sections**

This is one of the most misunderstood concepts.

***

### **Static Sections**

These sections are **hardcoded** into theme.liquid or template files.

Examples:

* Header
* Footer
* Main product section
* Main collection section

Static sections:

* Cannot be added or removed
* Cannot be moved by merchants
* Always appear on the page

Example:

```
{% section 'header' %}
```

***

### **Dynamic Sections**

These appear inside **JSON templates** and are merchant-controlled.

Merchants can:

* Add them
* Remove them
* Reorder them
* Customize them

This is the backbone of customizable layouts in OS 2.0.

Example JSON template:

```json
{
  "sections": {
    "main": { "type": "main-product" },
    "166123": { "type": "featured-collection" }
  },
  "order": [
    "main",
    "166123"
  ]
}
```

***

## **7. How JSON Templates and Sections Work Together**

JSON templates don’t contain markup.\
They contain **a list of sections** to render.

Example: `product.json`

```json
{
  "sections": {
    "main-product": {
      "type": "main-product"
    },
    "product-reviews": {
      "type": "reviews"
    }
  },
  "order": ["main-product", "product-reviews"]
}
```

This means:

* You define the layout
* Shopify injects the sections
* Merchants reorder them visually
* Apps can inject blocks (if allowed)

JSON templates are the reason sections became the heart of Shopify’s modular architecture.

***

## **8. App Blocks — A Game-Changer in OS 2.0**

Apps can now inject blocks into theme sections.\
This replaced the old, messy script-tag injection approach.

Apps register blocks in their extension:

* Reviews
* Upsells
* Shoppable videos
* Loyalty badges
* Product recommendations

Developers don’t need to modify theme code manually.

***

## **9. Section Presets — How Sections Appear in Theme Editor**

Every section should include a `presets` key:

```json
"presets": [
  {
    "name": "Featured Collection"
  }
]
```

This determines:

* The name shown in the “Add section” menu
* The default state of the section
* Whether merchants can add it to pages

If a section has **no preset**, it won’t appear in the Add Section panel.

***

## **10. The Lifecycle of a Section (From Creation to Use)**

Here's how sections come to life:

#### **1. Developer creates a section**

Inside `/sections/my-section.liquid`

#### **2. Adds settings and blocks**

The schema defines customization capability.

#### **3. Adds a preset**

So merchants can find it.

#### **4. JSON templates add section references**

Pages include and order sections.

#### **5. Merchant customizes sections visually**

Through the Theme Editor.

#### **6. Shopify stores values in settings\_data.json**

Behind the scenes.

***

## **11. Best Practices for Building Sections & Blocks**

#### ✔️ Keep sections small and purposeful

One section = one clear function.

#### ✔️ Use snippets inside sections

Helps maintain clean, reusable code.

#### ✔️ Make everything customizable

Merchants love flexibility.

#### ✔️ Leverage blocks smartly

Blocks = micro customizability.

#### ✔️ Avoid hardcoding content

Always use settings where applicable.

#### ✔️ Use unique, predictable IDs

Avoid confusing block types.

#### ✔️ Include meaningful presets

Otherwise merchants won’t find your section.

***

## **12. Common Mistakes Beginners Make (And How to Avoid Them)**

❌ Putting too many responsibilities in one section\
→ Break it into multiple smaller sections.

❌ Forgetting presets\
→ Section won’t show up in Theme Editor.

❌ Not using blocks when needed\
→ Merchants lose flexibility.

❌ Hardcoding HTML content\
→ Always use settings and block fields.

❌ Not testing on JSON templates\
→ Dynamic sections may misbehave.

❌ Forgetting that block limit = 50\
→ Too many blocks → poor performance.

***

## **Conclusion — You Now Master the Most Important Part of Shopify Themes**

Sections & Blocks are the **engine** behind modern Shopify theme development.

You now understand:

* Why sections exist
* How they evolved
* Anatomy of a section
* How blocks work
* Static vs dynamic sections
* How JSON templates use sections
* How presets expose sections to merchants
* Best practices
* Common pitfalls

With these concepts mastered, you’re ready to:

* Build beautiful modular sections
* Create fully dynamic themes
* Empower merchants with customization
* Understand any OS 2.0 theme’s architecture
* Create themes for clients or the Shopify Theme Store
