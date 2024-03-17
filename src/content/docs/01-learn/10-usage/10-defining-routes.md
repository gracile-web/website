# Defining routes

Like every meta-framework, routes are the central concept of Gracile.  
This is where everything got tied together, and besides that, and add-ons, there aren't many opinions left.

Gracile comes with a dedicated function that will take care of
typings as with JS or TS, and nothing more.

Under the hood, it uses the [`URLPattern` API](https://developer.mozilla.org/en-US/docs/Web/API/URLPattern) which is using converted patterns like that:

- `/src/routes/foo/[param]/foo.ts` => `/foo/:param/foo`
- `/src/routes/bar/[...path]` => `/bar/:path*`

## Ignored files and directories

- **Client**-side sibling **scripts** (`*.client.{js,ts}`)
- Leading **underscore** `_*.{js,ts}`, `_my-dir/*`
- Leading **dot** files/directories (hidden).

## `defineRoute` parameters

### `document`

Provides the [base document](/docs/learn/usage/defining-base-document/) for the route's page.  
Given:

```ts twoslash
// @filename: /src/document.ts

import { html } from '@lit-labs/ssr';

// [!code word:defaultDocument:1]
export const defaultDocument = (options: { url: URL }) => html`
  <html>
    <head>
      <!-- ... -->
      <title>${url.pathname}</title>
    </head>
    <body>
      <page-outlet></page-outlet>
    </body>
  </html>
`;

// @filename: /src/routes/my-page.ts

import { html } from 'lit';
import { defineRoute } from '@gracile/server/route';
import { defaultDocument } from '../document.js'; // [!code highlight]

export default defineRoute({
  document: defaultDocument, // [!code highlight]

  page: () => html`...`,
});
```

### `page`

Provides page template.

```ts twoslash
// @filename: /src/routes/my-page.ts

import { html } from 'lit';
import { defineRoute } from '@gracile/server/route';

export default defineRoute({
  // ...
  page: (context) => html`
    <main>
      <article class="prose">Hello</article>
    </main>
  `,
});
```

### `staticPaths`

Used with [**static** mode](/docs/learn/usage/output-modes/#doc_static-ssg) only.

You can provide `props` and `params` for populating page data.

**Page** and **document** contexts will be properly typed with your function return signature.

```ts twoslash
// @filename: /src/document.ts
import { html } from '@lit-labs/ssr';

export const defaultDocument = (options: { url: URL }) => html`...`;

//---cut---
// @filename: /src/routes/[...path].ts

import { html } from 'lit';
import { defineRoute } from '@gracile/server/route';

import { defaultDocument } from '../document.js';

export default defineRoute({
  document: (context) => defaultDocument(context),

  staticPaths: () =>
    [
      {
        params: { path: 'my/first-cat' },
        props: { cat: 'Kino' },
      },
      {
        params: { path: 'my/second-cat' },
        props: { cat: 'Valentine' },
      },
    ] as const,

  // NOTE: Hover the tokens to see the typings reflection.
  page: async (context) => html`
    <h1>${context.props.cat}</h1>
    <main>${url.pathname}</main>
    <footer>${context.params.path}</footer>
  `,
});
```

### `handler` (experimental)

Used with [**server** mode](/docs/learn/usage/output-modes/#doc_server-soon) only.

There are **two behavior** for the handlers:

**Returning an instance of `Response`** will terminate the pipeline, without going through
the page rendering that happens afterward otherwise.  
Useful for redirects, pure JSON API routes…

**Returning anything else** will provide the typed `props` for the page to consume.

See also the ["Forms" recipe](http://localhost:9898/docs/recipes/working-with-forms/).

```ts twoslash
// @filename: /src/document.ts
import { html } from '@lit-labs/ssr';
export const defaultDocument = () => html`...`;
// ---cut---

// @filename: /src/routes/index.ts

import { html } from 'lit';
import { defineRoute, jsonResponse } from '@gracile/server/route';
import { defaultDocument } from '../document.js';

const achievements = [{ name: 'initial' }];

export default defineRoute({
  // [!code word:handler:2]
  // [!code word:POST:2]
  handler: {
    POST: async (context) => {
      const formData = await context.request.formData();

      const name = formData.get('achievement')?.toString());

      if (name) achievements.push({ name });

      return Response.redirect(context.url, 303);
    },

    // [!code word:DELETE:2]
    // [!code word:jsonResponse:2]
    DELETE: () => {
      // NOTE: This is a short-hand for making a new Reponse(...) with appropriate options.
      return jsonResponse({ theEntireDatabase: true });
    },
  },

  document: defaultDocument,

  page: async (context) => html`
    <form method="post">
      <input type="text" name="achievement" />
      <button>Add achievement</button>
    </form>

    ${context.request.method}

    <ul>
      ${achievements.map((a) => html`<li>${a.name}</li>`)}
    </ul>

    <script type="module">
      const result = await fetch('/', { method: 'DELETE' }).then((r) =>
        r.json(),
      );
      console.log(result);
    </script>
  `,
});
```

## Per-route assets

As seen in the example above, each tag will be an entry point for bundles.  
Want to split? Add a tag. Want to group and optimize multiple modules together?
Just import them from a unique entry point, Rollup will do the rest.

```html
<link rel="stylesheet" href="/src/routes/my-page.scss" />
<script type="module" src="/src/routes/my-page.client.ts"></script>
```

If you're concerned about module script locations, which will happen right after the opening `<body>` of the containing document, note that after build, Rollup
will put them at the "proper" place before the closing `</body>`.  
Inline scripts, styles and CSS links will be left in place.

> [!IMPORTANT]  
> While it is strongly recommended to use `.js` even for TS, and **relative** paths,
> not absolute,
> these rules **won't apply in HTML** when using Vite / Rollup based
> systems.
> You have to provide the **full real path** relative to the project root, like
> `/src/components/my-element.ts`.  
> Same for non-vanilla CSS.

<!-- TODO: Separate "Assets section" -->
<!-- TODO: Explanation for "as-is" assets, e.g.: -->
<!-- await import(
  /* @vite-ignore */
  new URL('../../pagefind/pagefind-ui.js', import.meta.url).href
); -->

### Minimal example

```ts twoslash
// @filename: /src/document.ts
import { html } from '@lit-labs/ssr';

export const defaultDocument = (options: { url: URL; title: string }) => html``;

// ---cut---
// @filename: /src/routes/my-page.ts

import { html } from 'lit';
import { defineRoute } from '@gracile/server/route';
import { defaultDocument } from '../document.js';

export default defineRoute({
  document: (context) => defaultDocument({ ...context, title: 'My Page' }),

  page: ({ url }) => html`
    <!-- CAUTION: Use the full real path (relative to the project root) -->
    <link rel="stylesheet" href="/src/routes/my-page.scss" />
    <script type="module" src="/src/routes/my-page.client.ts"></script>

    <main class="content">
      <article class="prose">
        <!-- ... -->
        Hello ${url.pathname}
        <!-- ... -->
      </article>
    </main>
  `,
});
```

## What to put in routes?

Data fetching, dispatching, per-route assets to bundle, components, templates, modules, features…

It's generally better to use routes as entry points and not put too much UI or logic in there, beside what's strictly needed to boostrap the page.  
Routes are kind of "magic", in the sense that you're not calling them yourself in your
code, but the framework will use them predictably.
Thankfully, Gracile isn't crowding top level module exports, but just the default one.  
While it adds a level of indentation, it avoids clashes with your module-scoped functions.  
This is a perfectly reasonable use of ESM default exports.  
No static analysis or extraction either, meaning your functions are not in silos and won't behave in unexpected ways due to custom pre-processing.
