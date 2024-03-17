## ⚙️  Configuration

## Configuration file

```ts twoslash
// @filename: /gracile.config.js

import { defineConfig } from '@gracile/gracile';

export default defineConfig({
  //
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
