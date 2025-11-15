# How to Create a “Coming Soon” or “Under Maintenance” Page in Shopify

When you’re building a new Shopify store, redesigning an existing one, or doing maintenance on a live shop, there’s one thing you absolutely need:

A **professional, branded, customizable “Coming Soon” or “Under Maintenance” page**.

Most people think you need custom code, apps, or redirects to build one.

But Shopify already gives you the perfect tool — **the password page**, powered by `password.json`.

In this article, you’ll learn how to transform Shopify’s simple password screen into a fully branded landing page with email capture, custom sections, and your own design… without any apps.

***

## **🚀 Why Use the Password Page as Your “Coming Soon” Page?**

Shopify’s password feature does more than protect your store.

With a bit of theme customization, it becomes a:

* launch countdown page
* early-access signup page
* temporary maintenance page
* private beta access site
* under-construction landing page
* hype-building “coming soon” page

And because you’re using a **native template**, it:

✔ Loads instantly\
✔ Is fully theme-compatible\
✔ Requires no apps\
✔ Works with your branding\
✔ Is editable via Theme Editor

You can turn it into anything you want.

***

## **📁 Step 1 — Understand Where the Password Page Lives**

Shopify themes include a password template:

```
templates/password.json
```

And a section file:

```
sections/password.liquid
```

#### These two files control:

* the structure
* the design
* the content
* the password form
* email capture area

In Shopify theme editor, you can customize it just like any other page.

***

## **🎨 Step 2 — Customize `password.json` to Build Your Layout**

Here’s what the default password.json looks like:

```json
{
  "sections": {
    "main": {
      "type": "password"
    }
  },
  "order": ["main"]
}
```

Now watch what happens when we redesign it as a Coming Soon page:

```json
{
  "sections": {
    "hero": {
      "type": "coming-soon-hero"
    },
    "newsletter": {
      "type": "email-signup"
    },
    "password-form": {
      "type": "password"
    }
  },
  "order": ["hero", "newsletter", "password-form"]
}
```

That’s it — instant **custom layout**.

Your `coming-soon-hero` and `email-signup` sections can be fully custom, including:

* images
* videos
* gradients
* launch messaging
* countdown timers
* brand elements

***

## **🧩 Step 3 — Enhance the Password Section (`password.liquid`)**

Inside `sections/password.liquid`, you can add:

* custom headings
* input styles
* background images
* animations
* schema settings
* social links

Example schema snippet:

```json
{
  "name": "Coming Soon Settings",
  "settings": [
    {
      "type": "text",
      "id": "title",
      "label": "Heading",
      "default": "We're Launching Soon!"
    },
    {
      "type": "image_picker",
      "id": "background",
      "label": "Background Image"
    }
  ]
}
```

This makes your password page **Theme Editor friendly**, even for non-developers.

***

## **🎬 Step 4 — Add High-Impact Visual Elements**

Here are ideas to elevate your Coming Soon page:

#### ✔ Hero image or background video

![Hero Example](https://via.placeholder.com/1200x500.png?text=Hero+Banner)

#### ✔ Countdown Timer

Creates urgency and hype.

#### ✔ Email Capture Form

Grow your list before launch.

#### ✔ Social Links

Drive traffic to Instagram/TikTok until you launch.

#### ✔ Logo + Brand Statement

Make it feel premium.

#### ✔ Custom Font & Color Styling

Match your theme’s vibe.

***

## **⚠ Important: A Critical Limitation You Must Know**

![Warning](https://via.placeholder.com/100x100.png?text=!)

If you're working on a **Shopify development store**, your custom password page **WILL NOT** appear.

Instead, Shopify forces a generic development password template.

This is a hard platform rule.

#### Custom password pages ONLY work on:

✔ Stores on a paid Shopify plan\
✔ Trial stores\
✔ Development stores **after transfer to a merchant**

So your custom Coming Soon page will work perfectly **on real stores**, but not on dev environments.

***

## **🧱 What You&#x20;**_**Can**_**&#x20;and&#x20;**_**Cannot**_**&#x20;Do on a Password Page**

#### ✔ Allowed

* Custom layout
* Custom sections
* Email captures
* Branding
* JS animations
* Countdown timers
* Social links
* Custom backgrounds
* Logos

#### ❌ Not Allowed

(A password page cannot access full storefront objects)

* Product data
* Cart data
* Customer data
* Collection filtering
* Dynamic product recommendations

This is expected and does NOT affect “coming soon” usage.

***

## **💡 Example Custom Coming Soon Layout**

Here’s a layout structure that works beautifully:

```
[ Background Image / Video ]
[ Logo Centered ]
[ “We’re Launching Soon” Headline ]
[ Short tagline ]
[ Countdown Timer ]
[ Email sign-up form ]
[ Social icons row ]
[ Password form (for staff only) ]
```

![Coming Soon Wireframe](https://via.placeholder.com/1100x500.png?text=Coming+Soon+Wireframe)

***

## **🎉 Final Thoughts — A Very Underrated Shopify Feature**

Turning Shopify’s `password.json` into a landing page is:

✔ Simple\
✔ Fast\
✔ Customizable\
✔ No apps required\
✔ Perfect for new stores or maintenance periods

It lets you:

* Build hype
* Collect emails
* Show a branded presence
* Communicate status updates
* Allow staff access without exposing the storefront

Most merchants don’t know this exists — but now _you_ do.
