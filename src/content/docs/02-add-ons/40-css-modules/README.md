# CSS Modules (standard)

Provides a CSSStyleSheet or a CSSResult (Lit) for use with [import attributes](https://tc39.es/proposal-import-attributes/).  
Using the "with" keyword and "type : css".

> [!WARNING]
> This section is under construction.

## Installation

```sh
npm i @gracile/css-modules
```

```ts twoslash
// @filename: /gracile.config.ts

import { defineConfig } from '@gracile/gracile';

import { viteCssModulesPlugin } from '@gracile/css-modules'; // [!code highlight]

export default defineConfig({
  // ...

  vite: {
    plugins: [
      viteCssModulesPlugin(), // [!code highlight]
      // ...
    ],
  },
});
```

---

Based on [vite-plugin-standard-css-modules](https://github.com/JulianCataldo/.vite-plugin-standard-css-modules).
