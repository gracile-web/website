# Open Graph images

Generate social sharing thumbnails for your websites, with plain **HTML** + **CSS** templates. \
Extract metadata from pages, on-the-fly (middleware) or from distributable (static folder).

> [!WARNING]
> This section is under construction.

## Installation

```sh
npm i @gracile/og-images
```

```ts twoslash
// @filename: /gracile.config.ts

import { defineConfig } from '@gracile/gracile';

import { viteOgImagesPlugin } from '@gracile/og-images/addon'; // [!code highlight]

export default defineConfig({
  // ...

  vite: {
    plugins: [
      viteOgImagesPlugin(),
      // ...
    ],
  },
});
```

## Usage

See the [og-images-generator](https://juliancataldo.github.io/og-images-generator/) documentation.

---

Based on [og-images-generator](https://github.com/JulianCataldo/og-images-generator).
