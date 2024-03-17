# Defining the base documents

Document are wrapping your routes and are streamed eagerly.  
They are housing critical assets, page metadata, hydration helpers, asset entry points…

## Minimal example

```ts twoslash
// @filename: /src/document.ts

// WARNING: We're using "html" from Lit labs' SSR here, not the regular Lit package.
import { html } from '@lit-labs/ssr';
import { helpers } from '@gracile/server/document';
import { createMetadata } from '@gracile/metadata';

const SITE_TITLE = 'My Website';

const criticalAssets = html`
  <script>
    {
      console.log('Hello');
      // Do critical stuff
    }
  </script>
`;

export const defaultDocument = (options: { url: URL; title: string }) => html`
  <!doctype html>
  <html lang="en">
    <head>
      <!-- Helpers -->
      ${helpers.fullHydration}

      <!-- Global assets -->
      <link rel="stylesheet" href="/src/styles/global.scss" />
      <script type="module" src="/src/document.client.ts"></script>

      <!-- Metadata -->
      ${createMetadata({
        siteTitle: SITE_TITLE,
        pageTitle: `${SITE_TITLE} | ${options.title}`,
        faviconUrl: '/public/favicon.svg',
        pageDescription: 'A cool website',
      })}

      <!-- Critical Assets -->
      ${criticalAssets}
    </head>

    <body>
      <!-- WARNING: Don't forget to add this. Otherwise your page won't be inserted. -->
      <page-outlet></page-outlet>
    </body>
  </html>
`;

// @filename: /src/routes/my-page.ts

import { html } from 'lit';
import { defineRoute } from '@gracile/server/route';
import { defaultDocument } from '../document.js';

export default defineRoute({
  document: (context) => defaultDocument({ ...context, title: 'My Page' }), // [!code highlight]

  // [!code word:GET]
  page: ({ url }) => html`
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

> [!IMPORTANT]
> You CAN nest **server** OR **regular** templates in **server** templates.  
> You CANNOT nest **server** templates in **regular** templates.

## Page outlet

`<page-outlet></page-outlet>` is a special tag, a placeholder that will be replaced with your page rendering.  
It's one of the very few "unholy" things Gracile is doing.  
However it is not meant as just an esthetic thing, compared to a more traditional comment marker (e.g. `<!--SSR_OUTLET-->`).  
Defined as a Custom Element, it might serve as a smart fallback or an ultimate error boundary in the future.
E.g. if the rendering fail, it could be used to show your own error overlay. It
can be an interesting pattern to explore.

`<slot></slot>` could have been used to, but it's a bit of a stretch of what a _real_ slot is, and can clash with legitimate slots.

## Notes

Gracile is purposedly separating the HTML base document with the rest of the
page rendering.  
It is because under the hood, it will render it beforehand and stream it to the
user, resulting in a faster response time.  
Usually there isn't too much work at this level, and without those premices, the
browser isn't kickstarting its work before receiving the real meat of the
markup, which can be longer to process streamed chunks.

Note that you'll benefit from looser error boundaries, too. When a sub-templates
fails to render, you still get the valid markup, instead of a giant crash.

Also base document is holding critical assets and Vite's magic sauce for dev.
Hence an additional reason to process it alone, in the pre-pass.

Finally, we are using `@lit-labs/ssr` own `html` tagged template literal, which
is different from the `lit`'s one.  
In short: it can handle all the "special" HTML tags (`<script>`, `<title>`,…)
while being faster to process, due to its non-interactive nature.  
It's also 1:1 HTML!  
Lit "regular" templates can also be used without the "DSL-y" bits (`@event`,
`.property`…) if you want to go you own way, like no JS, vanilla JS, Alpine.js, etc.

You'll find that the "Lit HTML" abstraction, over-all, is tiny, and totally opt-in for
the "special" stuff, like `@event`, property `.binding`…

---

It's better to separate the document from your route, because each of Lit and Lit SSR `html` tagged template will clash otherwise.  
It's totally possible to rename it through import though, like `server.html`,
but you will probably lose formatting, syntax highlighting, TypeScript Lit Plugin intellisense, etc.

Keep in mind you can use conditional logic inside your base document, so you can
alter metadata, assets loading etc. to cater for each routes.

Most projects won't need to define multiple documents, but if you find yourself
having too much divergences, or for other reasons like security, you have the
flexibilty to do that.

Gracile isn't aware of anything other than you routes files, you can locate and name documents, components, etc. with you own conventions.
