# ⚙️ Configuration

There aren't many things to configure with Gracile, and most of your endeavors are achievable through the Vite/Rollup configuration API.

## Configuration file

```ts twoslash
// @filename: /gracile.config.js

import { defineConfig } from '@gracile/gracile';

export default defineConfig({
  //

  vite: {
    // ...
  },
});
```

## Directory structure

The bare minimum is a `src/routes/<...>.ts` file.

<div class="file-tree">

- gracile.config.js
- src
  - routes
    - index.ts
    - foo
      - [myParam].ts
      - bar
        - [..restParams].ts

</div>
