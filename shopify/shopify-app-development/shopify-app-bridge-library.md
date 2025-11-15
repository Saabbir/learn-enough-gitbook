# Shopify App Bridge 101: What It Is, Why It Matters, and How to Use It

If you’ve ever opened a Shopify embedded app inside the Shopify Admin, you’ve already interacted with **Shopify App Bridge** — even if you didn’t know it.

App Bridge is the invisible layer that connects your app to the Shopify Admin, letting you:

* Read store information
* Render UI components inside the Shopify Admin
* Trigger actions like toasts, modals, or save bars
* Navigate the admin UI
* Access user/session data
* Provide a seamless merchant experience

Without App Bridge, embedded apps would feel like separate websites.\
With App Bridge, they become **tight, native extensions inside Shopify**.

Let’s break it all down.

***

## **📚 Table of Contents**

1. What Is Shopify App Bridge?
2. Why Shopify Apps Need App Bridge
3. How App Bridge Works (In Plain English)
4. Installing App Bridge
5. The `shopify` Global Variable
6. Your First App Bridge API Call
7. Useful App Bridge APIs
8. App Bridge Web Components
9. App Bridge React Components & Hooks
10. Final Thoughts

***

## **1. What Is Shopify App Bridge?**

**Shopify App Bridge is a JavaScript SDK for embedded Shopify apps.**

Whenever your app loads inside Shopify Admin, it’s displayed inside:

* an **iframe** (desktop admin)
* a **WebView** (Shopify Mobile app)

Inside that frame, App Bridge allows your app to:

✔ communicate with Shopify\
✔ render UI elements in the admin\
✔ access store/user metadata\
✔ control navigation\
✔ trigger admin UI elements like toasts, modals, and title bars

***

## **2. Why Shopify Apps Need App Bridge**

App Bridge makes your embedded app:

#### **More integrated**

Your app can render Shopify-native UI like:

* Navigation menu
* Save bar
* Title bar
* Modals
* Resource pickers

#### **More secure**

Defers authentication and permissions through Shopify.

#### **More consistent**

Your app feels like part of the Shopify admin, not an external website.

#### **More powerful**

App Bridge gives direct access to:

* shop domain
* API key
* user metadata
* API session details

***

## **3. How App Bridge Works (In Plain English)**

When your app loads inside an iframe:

1. Shopify injects information into your app (shop, host, user token)
2. App Bridge initializes using your API key
3. Your app gains access to global `shopify` APIs
4.  You can now call methods like:

    ```js
    shopify.toast.show("Hello!")
    shopify.resourcePicker({ type: "product" })
    shopify.modal.show("modal-id")
    ```

That’s it.

App Bridge acts as a **bridge** between:

* your **app code**
* Shopify's **admin UI**
* the **merchant**

***

## **4. Installing App Bridge**

#### If you used the **Shopify Remix App Template**

App Bridge is already installed and configured — no setup needed.

***

#### If you built your app manually

Add this inside `<head>`:

```html
<meta name="shopify-api-key" content="%SHOPIFY_API_KEY%" />
<script src="https://cdn.shopify.com/shopifycloud/app-bridge.js"></script>
```

This script:

* loads directly from Shopify
* stays up-to-date automatically
* initializes the App Bridge runtime

Once included, your app gets the global `shopify` object.

***

## **5. The `shopify` Global Variable**

After initialization, you can access App Bridge APIs using:

```js
shopify
```

This global variable contains everything.

#### How to inspect it:

1. Open Shopify Admin
2. Open your embedded app
3. Open Chrome DevTools
4. Select the **iframe** of your app
5. Type `shopify` in console

You’ll see all the API namespaces like:

* `shopify.toast`
* `shopify.modal`
* `shopify.resourcePicker`
* `shopify.config`
* `shopify.user`

***

## **6. Your First App Bridge API Call**

One of the most commonly used APIs is the **Resource Picker**.

Here’s a working example:

```html
<!DOCTYPE html>
<head>
  <meta name="shopify-api-key" content="%SHOPIFY_API_KEY%" />
  <script src="https://cdn.shopify.com/shopifycloud/app-bridge.js"></script>
</head>

<body>
  <button id="open-picker">Open resource picker</button>

  <script>
    document
      .getElementById("open-picker")
      .addEventListener("click", async () => {
        const selected = await shopify.resourcePicker({ type: "product" });
        console.log(selected);
      });
  </script>
</body>
```

This opens a native product selector directly inside Shopify Admin — no custom UI required.

***

## **7. Useful App Bridge APIs (Most Common Ones)**

App Bridge provides a ton of APIs — here are the most important ones you’ll use daily.

***

### **✔ 1. Retrieve Config Details**

```js
shopify.config.shop;     // shop domain
shopify.config.apiKey;   // API key used at runtime
```

***

### **✔ 2. Resource Picker**

Select products, collections, or media.

```js
const selected = await shopify.resourcePicker({
  type: "product",
  multiple: true,
});
```

***

### **✔ 3. Toast Notifications**

```js
shopify.toast.show("Message sent");
```

***

### **✔ 4. Modals**

```js
shopify.modal.show("my-modal");
```

And in HTML:

```html
<ui-modal id="my-modal"></ui-modal>
```

***

### **✔ 5. User Data**

Fetch details about the currently logged-in Shopify user:

```js
const user = await shopify.user();
```

***

## **8. App Bridge Web Components**

The latest App Bridge is built on **Web Components**, meaning you can use native HTML tags to render Shopify admin UI elements.

Common components:

```html
<ui-modal></ui-modal>
<ui-nav-menu></ui-nav-menu>
<ui-save-bar></ui-save-bar>
<ui-title-bar></ui-title-bar>
```

These elements render into Shopify-native UI inside the admin.

***

## **9. App Bridge React Components & Hooks**

If you are building a React-based Shopify app, you should use the **App Bridge React package**.

#### React Components:

```jsx
<Modal />
<NavMenu />
<SaveBar />
<TitleBar />
```

#### React Hook:

```js
import { useAppBridge } from '@shopify/app-bridge-react';

const shopify = useAppBridge();
shopify.toast.show("Some message");
```

These components give you:

* simplified APIs
* reactivity
* declarative UI
* frictionless admin integration

Perfect for modern Shopify app development.

***

## **10. Final Thoughts**

Shopify App Bridge is one of the most important technologies in the Shopify ecosystem. It’s what makes apps feel **native**, **consistent**, and **integrated** inside Shopify Admin.

By understanding App Bridge, you unlock the ability to:

✔ Build seamless embedded experiences\
✔ Trigger native UI components\
✔ Communicate with the Shopify host\
✔ Access critical information\
✔ Use Web Components or React\
✔ Build apps merchants trust and enjoy

App Bridge is not just a library — it’s the foundation of modern Shopify app UX.
