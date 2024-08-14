# Gracile/Vite configuration

API references extracted from the Gracile code base.  
Examples, functions, classes, constants, type declarations…

## Interface: GracileConfig

**Example**

`/vite.config.js`

```js
import { gracile } from '@gracile/gracile/plugin';
import { defineConfig } from 'vite';

export default defineConfig({
  plugins: [
    gracile({
      output: 'server',

      dev: {
        locals: (_context) => {
          return {
            requestId: crypto.randomUUID(),
            userEmail: 'admin@admin.home.arpa',
          };
        },
      },

      routes: {
        exclude: ['**/a-defective-route.ts'],
      },
    }),
  ],
});
```

**Properties**

| Property          | Type                               | Default value | Description                                                                                                                                      | Defined in                               |
| ----------------- | ---------------------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------- |
| `dev?`            | `object`                           | `undefined`   | Settings for the development mode.                                                                                                               | packages/engine/dist/user-config.d.ts:43 |
| `dev.locals?`     | (`context`: `object`) => `unknown` | `undefined`   | Get incoming request context and apply locals for the Gracile request handler. Useful for mocking the production server. For `server` mode only. | packages/engine/dist/user-config.d.ts:50 |
| `output?`         | `"static"` \| `"server"`           | `'static'`    | The target output for the build phase. See the [documentation](/docs/learn/usage/output-modes/).                                                 | packages/engine/dist/user-config.d.ts:39 |
| `routes?`         | `object`                           | `undefined`   | Settings for routes in `/src/routes`.                                                                                                            | packages/engine/dist/user-config.d.ts:57 |
| `routes.exclude?` | `string`[]                         | `undefined`   | Exclude routes with an array of patterns. Useful for debugging.                                                                                  | packages/engine/dist/user-config.d.ts:61 |

## Function: gracile()

```ts
function gracile(config?): any[];
```

The main Vite plugin for loading the Gracile framework.

**Parameters**

<div class="typedoc-table"><table>
<tr>
<th>Parameter</th>
<th>Type</th>
<th>Description</th>
</tr>
<tr>
<td>

`config`?

</td>
<td>

[`GracileConfig`](#doc_interface-gracileconfig)

</td>
<td>

Gracile configuration.

</td>
</tr>
</table></div>

**Returns**

`any`[]

Vite plugins. `any` is used to prevent Vite typings version mismatches for the plugin API.

**Example**

`/vite.config.js`

```js
import { gracile } from '@gracile/gracile/plugin';
import { defineConfig } from 'vite';

export default defineConfig({
  plugins: [gracile({ output: 'server' })],
});
```

**Defined in**

packages/engine/dist/plugin.d.ts:20
