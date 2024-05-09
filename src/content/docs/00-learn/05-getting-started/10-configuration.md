# ⚙️ Configuration

There aren't many things (yet) to configure with Gracile, and most of your endeavors are achievable through the Vite/Rollup configuration API.

## Configuration file

The `gracile.config` (`.js` or `.ts`) is where you put the Gracile specifics and Vite options.  
Can be a **TypeScript** or a **JavaScript** file.

```ts twoslash
// @filename: /gracile.config.ts

import { defineConfig } from '@gracile/gracile';

export default defineConfig({
  port: 4567,

  vite: {
    plugins: [
      // ...
    ],
  },
});

// -----------------------------------------------------------------------------
```

> [!TIP]  
> This file is totally **optional**!

### Capabilities

#### TypeScript

Under the hood, The TypeScript code and **all imported modules** get transpiled on the fly, that's it:
You can enjoy writing your own plugins here with parity with
your application code regarding type-safety and with easy module sharing (think app-wide constants).

#### Automatic-reloading

Every time the configuration is changed or when its imported modules get updated, Gracile will reload itself.  
This also works for third parties in the `node_modules` folder.

## Directory structure

The bare minimum is a `src/routes/<...>.ts` file.

<div class="file-tree">

- my-project/
  - gracile.config.ts _- Base config._
  - public/ _- Static assets._
    - favicon.svg
  - src/
    - routes/
      - index.ts _- At least one route is required_
      - foo/
        - [myParam].ts
        - bar/
          - [..restParams].ts
  - ...

</div>

You will need to [define and put a default `document`](/docs/learn/usage/defining-base-document/) somewhere, too. For example as `src/document.ts`.

There are **no other** directory structure conventions besides the file router, the root config. and the `public` assets folder.
