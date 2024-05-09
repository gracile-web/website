# SVG

Import, auto-optimize, and inline SVG files in your HTML templates.

> [!WARNING]
> This section is under construction.

## Installation

```sh
npm i @gracile/svg
```

```ts twoslash
// @filename: /gracile.config.ts

import { defineConfig } from '@gracile/gracile';

import { viteSvgPlugin } from '@gracile/svg/vite'; // [!code highlight]

export default defineConfig({
  // ...

  vite: {
    plugins: [
      viteSvgPlugin({
        // SVGO options…
      }),
      // ...
    ],
  },
});
```

## Usage

```ts twoslash
// @filename: /src/modules/my-partial.ts

import { html } from 'lit';

import myIcon from '../assets/icons/my-icon.svg' with { type: 'svg-lit' }; // [!code highlight]

export const myPartial = html`
  <!-- -->
  <div>${myIcon}</div>
`;
```
