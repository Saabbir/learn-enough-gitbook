# Shopify Theme Development

## Shopify Theme Development

Learn everything you need to get started with Shopify theme development. All the tips, tricks, and notes are shared!

## Shopify theme d**irectory structure**

You can run certain theme commands, such as `shopify theme dev`, only if the directory you're using matches the default Shopify theme [directory structure](https://shopify.dev/docs/themes/architecture#directory-structure-and-component-types).

The default Shopify theme directory structure is as follows:

```jsx
// Shopify theme directory structure

├── assets
├── config
├── layout
├── locales
├── sections
├── snippets
└── templates
    └── customers
```

_Subdirectories, other than the ones listed (customers), aren't supported._

To see an example of a complete theme directory structure, and the various component types, explore the [Dawn GitHub repository](https://github.com/Shopify/dawn).

Learn more about Shopify theme directory structure in here

→ [https://shopify.dev/docs/themes/architecture](https://shopify.dev/docs/themes/architecture)

> Only a `layout` directory containing a `theme.liquid` file is required for the theme to be uploaded to Shopify.

> The `.github` folder in the theme repository is ignored.

## Templates

Templates control what's rendered on each type of page in a theme.

**No template types are required.** However, you must have a matching template for any page type that you want to render. For example, to render a product page, you need at least one template of type `product`.

```jsx
// You can use the following template types in your theme.

└── theme
	  └── layout
	  └── templates
				└── 404
				└── article
				└── blog
				└── cart
				└── collection
				└── customers
						└── account
						└── activate_account
						└── addresses
						└── login
						└── order
						└── register
						└── reset_password
				└── gift_card.liquid
				└── index
				└── list-collections
				└── page
				└── password
				└── product
				└── robots.txt.liquid
				└── search
				└── metaobject
```

Learn more about different template types

→ [https://shopify.dev/docs/themes/architecture/templates#template-types](https://shopify.dev/docs/themes/architecture/templates#template-types)

There are two different file types you can use for a theme template: **JSON and Liquid**. Some template types support only the Liquid file type, while other template types support either template file type.

JSON templates provide more flexibility for merchants to **add, remove, and reorder sections**, including app sections.

A Liquid template doesn't have a **fixed schema** while a JSON template accepts only a JSON file with a fixed schema **and list of accepted attributes**.

A Liquid template accepts standard HTML and Liquid. Liquid templates can access any [global Liquid objects](https://shopify.dev/docs/api/liquid/objects), as well as the object that's associated with the template.

> If you want to use [sections](https://shopify.dev/docs/themes/architecture/sections) in a template, then you should use a JSON template.

> You can have a maximum of 1000 JSON templates in your theme, across all template types.

> The `gift_card` and `robots.txt` templates can't be JSON templates, so you must make them Liquid templates.

## **JSON templates**

JSON templates allow you to control the look and feel of different pages of the online store using [sections](https://shopify.dev/docs/themes/architecture/sections).

JSON templates are data files that store a list of sections to be rendered, and their associated settings. Merchants can add, remove, and reorder these sections using the theme editor.

> JSON templates can render up to 25 sections, and each section can have up to 50 blocks.

## Dawn theme

Dawn is used as the basis of all [free Shopify themes](https://themes.shopify.com/collections/free-themes). Development stores created after June 29, 2021 use Dawn as their default theme. You can also install Dawn directly from the [Shopify Theme Store](https://themes.shopify.com/themes/dawn/styles/default).

> If you're building a theme for the Shopify Theme Store, then you can use Dawn as a starting point. However, the theme that you submit needs to be [substantively different from Dawn](https://shopify.dev/docs/themes/store/requirements#uniqueness) so that it provides added value for users.

## **Shopify CLI commands for themes**

Run `shopify theme` in the terminal to see all the available commands for Shopify theme development.

```jsx
USAGE
  $ shopify theme COMMAND

COMMANDS
  theme check            Validate the theme.
  theme console          Shopify Liquid REPL (read-eval-print loop) tool
  theme delete           Delete remote themes from the connected store. This command can't be undone.
  theme dev              Uploads the current theme as a development theme to the connected store, then prints theme editor
                         and preview URLs to your terminal. While running, changes will push to the store in real time.
  theme info             Print basic information about your theme environment.
  theme init             Clones a Git repository to use as a starting point for building a new theme.
  theme language-server  Start a Language Server Protocol server.
  theme list             Lists your remote themes.
  theme open             Opens the preview of your remote theme.
  theme package          Package your theme into a .zip file, ready to upload to the Online Store.
  theme publish          Set a remote theme as the live theme.
  theme pull             Download your remote theme files locally.
  theme push             Uploads your local theme files to the connected store, overwriting the remote version if specified.
  theme rename           Renames an existing theme.
  theme share            Creates a shareable, unpublished, and new theme on your theme library with a randomized name. Works
                         like an alias to `shopify theme push -u -t=RANDOMIZED_NAME`.
```

Learn more → [Shopify CLI commands for themes](https://shopify.dev/docs/themes/tools/cli/commands)

## **Choosing between Shopify CLI and Theme Kit**

Shopify CLI replaces Theme Kit for most Shopify theme development tasks. You should use Shopify CLI if you're working on [Online Store 2.0](https://shopify.dev/docs/themes/os20) themes. You should use Theme Kit instead of Shopify CLI only if you're working on older themes.

## Getting started with the theme development

```jsx
// Init a theme with the default dawn theme
shopify theme init

// This will install the specified theme as a starter theme
shopify theme init --clone-url GITHUB_REPO_URL

// cd to the new theme
cd "my-new-theme"

// Connecting to a store and start the local development
shopify theme dev --store STORE_NAME.myshopify.com

// First time upload you theme to a store
shopify theme push --unpublished

// After that just push
shopify theme push

// Publish your theme
shopify theme publish

// Download a theme from the store
shopify theme pull --store STORE_NAME.myshopify.com
```

Learn more about getting started with local theme development in here

→ [https://shopify.dev/docs/themes/getting-started/create](https://shopify.dev/docs/themes/getting-started/create)

→ [https://shopify.dev/docs/themes/getting-started/customize](https://shopify.dev/docs/themes/getting-started/customize)

> If you haven't done so already, you're prompted to log in to Shopify when you run the `pull` command. Make sure that you log in using the account that was granted access to the store. If you're already logged in with an account that doesn't have appropriate access, then you can log out using `shopify auth logout`.

> Preview link generated using `shopify theme dev` will only work in Google Chrome.

## Custom password template

[Password protection](https://help.shopify.com/en/manual/online-store/themes/password-page)

You can customize Shopify **password.json** template to serve as a website down (under maintenance) page or email capture page like below screenshot.

![https://shopify.dev/assets/themes/templates/password.png](https://shopify.dev/assets/themes/templates/password.png)

[password](https://shopify.dev/docs/themes/architecture/templates/password)

> If you're working on a [development store](https://shopify.dev/docs/themes/tools/development-stores), then you can't show a custom password page on the store. A development store-specific password page is always displayed.

## Shopify AJAX

### Add to cart

```jsx
const body = new FormData(form);
body.append("sections", "section-name"); // optionally ask for any section html

fetch("/cart/add.js", {
  method: "post",
  body,
}
```

### Update cart

You can remove items from the cart by setting the quantity to 0.

```jsx
const line_item_key = "47844824351002:f72553800510cc393b720e258eb5cf09"
const qty = 1;

fetch("/cart/update.js", {
  method: "post",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    updates: {
      [line_item_key]: qty, // qty value 0 will remove this from cart
    },
    sections: "section-1, section-2", // optionally ask for any section html
  }),
}
```

### Clear cart

```jsx

fetch("/cart/clear.js", {
  method: "post",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    sections: "section-1", // optionally ask for any section html
  }),
}

```

.\
.\
.\
.\
.\
.\


_✍️ More to be written soon!_
