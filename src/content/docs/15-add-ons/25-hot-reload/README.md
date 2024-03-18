# Hot-reload (experimental)

You already get a pretty good hot reload experience with Gracile thanks
to its Vite foundation.  
However you might want to preserve the state of your Web Components without
doing a full page reload on each change.

> [!WARNING]
> This section is under construction.

## Installation

```sh
npm i @gracile/hot-reload
```

```ts twoslash
// @filename: /gracile.config.ts

import { defineConfig } from '@gracile/gracile';

import { viteHotReloadPlugin } from '@gracile/hot-reload/addon'; // [!code highlight]

export default defineConfig({
  // ...

  vite: {
    plugins: [
      viteHotReloadPlugin(),
      // ...
    ],
  },
});
```

---

Based on [vite-plugin-web-components-hmr](https://github.com/fi3ework/vite-plugin-web-components-hmr) which is also based on [@open-wc/dev-server-hmr](https://github.com/open-wc/open-wc/tree/c5444f79ac863d68abdbf84e5c49d9b07223bd1c/packages/dev-server-hmr).
