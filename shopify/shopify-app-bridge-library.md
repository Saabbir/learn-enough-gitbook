# Shopify App Bridge Library

## Shopify App Bridge Library

App Bridge is the JavaScript SDK for [Embedded Apps](https://shopify.dev/docs/apps/admin/embedded-app-home), providing access to data and UI rendering within the Shopify Admin.

### **More About Shopify App Bridge Library**

The App Bridge library provides APIs that enable Shopify apps to render UI in the Shopify [embedded app home](https://shopify.dev/docs/apps/build/admin) surface.

Apps built with Shopify App Bridge are more performant, flexible, and seamlessly integrate with the Shopify admin. You can use Shopify App Bridge with [Polaris](https://polaris.shopify.com/) to provide a consistent and intuitive user experience that matches the rest of the Shopify admin.

App Bridge enables you to do the following from your embedded app home:

* Render a [navigation menu](https://shopify.dev/docs/api/app-bridge-library/web-components/ui-nav-menu) on the left of the Shopify admin.
* Render a [contextual save bar](https://shopify.dev/docs/api/app-bridge-library/apis/contextual-save-bar) above the top bar of the Shopify admin.
* Render a [title bar](https://shopify.dev/docs/api/app-bridge-library/web-components/ui-title-bar) with primary and secondary actions.

The latest version of App Bridge is built on top of web components and APIs to provide a flexible and familiar development environment. Your app can invoke these [APIs](https://shopify.dev/docs/api/app-bridge-library) using vanilla JavaScript functions.

On the web, your app renders in an iframe and in the [Shopify mobile app](https://www.shopify.com/install) it renders in a WebView.

![App Bridge Example](../.gitbook/assets/app-bridge-example.png)

```
                                                 *Pink boxes arrive via App Bridge Library.*
```

[About Shopify App Bridge](https://shopify.dev/docs/api/app-bridge)

[App Bridge](https://shopify.dev/docs/api/app-bridge-library)

## Getting started

If you created your app using the [Shopify Remix App template](https://github.com/Shopify/shopify-app-template-remix), then your app is already set up with App Bridge.

If not, you can add App Bridge to your app by including the `app-bridge.js` script tag and your `apiKey` as seen in the below example.

```html
<head>
  <meta name="shopify-api-key" content="%SHOPIFY_API_KEY%" />
  <script src="https://cdn.shopify.com/shopifycloud/app-bridge.js"></script>
</head>
```

The `app-bridge.js` script loads directly from Shopify and automatically keeps itself up-to-date so you can start building right away.

## **Global variable**

After App Bridge is set up in your app, you have access to the `shopify` global variable. This variable exposes various App Bridge functionalities, such as [displaying toast notifications](https://shopify.dev/docs/api/app-bridge-library/apis/toast) or [retrieving app configuration details](https://shopify.dev/docs/api/app-bridge-library/apis/config).

To explore all the functionality available on the `shopify` global variable:

1. Open the Chrome developer console while in the Shopify admin.
2. Switch the frame context to your app's `iframe`.
3. Enter `shopify` in the console.

## Your first API call

The following example uses [`Resource Picker`](https://shopify.dev/docs/api/app-bridge-library/apis/resource-picker) to open a UI component that enables users to browse, find, and select products from their store using a familiar experiences.

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

## Shopify App Bridge features

### APIs

Shopify App Bridge Library provides some useful APIs under\*\*`shopify`\*\* global object for us to use.

Like **`shopify.toast`** API to show action messages.

```jsx
// Get config details
shopify.config.shop; // => 'your-shop-name.myshopify.com'
shopify.config.apiKey;

// Product picker with multiple selection
const selected = await shopify.resourcePicker({
  type: "product",
  multiple: true,
});

// Show toast
shopify.toast.show("Message sent");

// Show modal
shopify.modal.show("modal-id"); // <ui-modal id="modal-id"></ui-modal>

// Get user data
await shopify.user();
```

Learn more at → [https://shopify.dev/docs/api/app-bridge-library/apis](https://shopify.dev/docs/api/app-bridge-library/apis)

### Web Components

```jsx
// Shopify App Bridge Library provides following web components
// for us to use inside the app code

<ui-modal></ui-modal>
<ui-nav-menu></ui-nav-menu>
<ui-save-bar></ui-save-bar>
<ui-title-bar></ui-title-bar>
```

Learn more at → [https://shopify.dev/docs/api/app-bridge-library/web-components](https://shopify.dev/docs/api/app-bridge-library/web-components)

### **React Components and Hooks**

Shopify App Bridge Library also has react and remix companion library which provides helpful components and hooks.

```jsx
// React components provides by companion react library

<Modal />
<NavMenu />
<SaveBar />
<TitleBar />

// React hook ( useAppBridge )

import { useAppBridge } from '@shopify/app-bridge-react';
const shopify = useAppBridge();
shopify.toast.show('Some messages');
```

Learn more at → [https://shopify.dev/docs/api/app-bridge-library/react-components](https://shopify.dev/docs/api/app-bridge-library/react-components)
