# Sitemap

Generate a site map for the web crawlers to collect.

> [!WARNING]
> This section is under construction.

## Installation

```sh
npm i @gracile/sitemap
```

```ts twoslash
// @filename: /gracile.config.ts

import { defineConfig } from '@gracile/gracile';

import { viteSitemapPlugin } from '@gracile/sitemap/addon'; // [!code highlight]

export default defineConfig({
  // ...

  vite: {
    plugins: [
      viteSitemapPlugin(),
      // ...
    ],
  },
});
```

---

Based on [sitemap](https://github.com/ekalinin/sitemap.js).
