# Theme App Extension

## Theme App Extension

[About theme app extensions](https://shopify.dev/docs/apps/build/online-store/theme-app-extensions)

## File structure

```bash
└── extensions
  └── my-theme-app-extension
      ├── assets
      ├── blocks
      ├── snippets
      ├── locales
      ├── package.json
      └── shopify.extension.toml
```

[Theme app extension configuration](https://shopify.dev/docs/apps/build/online-store/theme-app-extensions/configuration)

## Start building

```bash
# Generate a theme extension
shopify app generate extension

# Preview extension on a given theme by providing theme id
shopify app dev --theme=168938701082 # use a staging theme id instead of the live one

# Deploy extension
shopify app deploy --version="1.0.0" --message="First deploy"
```

[Build theme app extensions](https://shopify.dev/docs/apps/build/online-store/theme-app-extensions/build)
