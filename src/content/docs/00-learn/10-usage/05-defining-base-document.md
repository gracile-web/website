# Defining the base documents

Document are wrapping your routes and are streamed eagerly.  
They are housing critical assets, page metadata, hydration helpers, asset entry points…

## Example

```ts twoslash
// @filename: /src/document.ts

// WARNING: We're using the server-only "html" from Lit SSR here, not the regular Lit package.

import { html } from '@gracile/gracile/server-html';
import { helpers } from '@gracile/gracile/document';
import { createMetadata } from '@gracile/metadata';

const SITE_TITLE = 'My Website';

const criticalAssets = html`
  <script>
    {
      const msg = 'Hello';
      console.log(msg);
      // Do critical stuff inline
    }
  </script>
`;

// NOTE: "url" is the bare minimum provided in the route context by Gracile.

export const document = (options: { url: URL; title?: string }) => html`
  <!doctype html>
  <html lang="en">
    <head>
      <!-- Helpers -->
      ${helpers.fullHydration}

      <!-- Global assets -->
      <!-- CAUTION: Use the full real path (relative to the project root) -->
      <link rel="stylesheet" href="/src/styles/global.scss" />
      <script type="module" src="/src/document.client.ts"></script>

      <!-- Metadata -->
      ${createMetadata({
        siteTitle: SITE_TITLE,
        pageTitle: `${SITE_TITLE} | ${options.title || 'Home'}`,
        faviconUrl: '/public/favicon.svg',
        pageDescription: 'A cool website',
      })}

      <!-- Critical Assets -->
      ${criticalAssets}

      <!-- Automatic page assets injection -->
      ${helpers.pageAssets}
    </head>

    <body>
      <!-- WARNING: Don't forget to add this. Otherwise your route's page template won't be inserted. -->
      <route-template-outlet></route-template-outlet>
    </body>
  </html>
`;

// @filename: /src/routes/my-page.ts

import { defineRoute } from '@gracile/gracile/route';
import { html } from 'lit';
// TIP: Alternatively, you can use the server-only "html" tag if you don't need hydration.
// import { html } from '@gracile/gracile/server-html';

import { document } from '../document.js';

export default defineRoute({
  document: (context) => document({ ...context, title: 'My Page' }), // [!code highlight]

  // [!code word:GET]
  template: ({ url }) => html`
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

`<route-template-outlet></route-template-outlet>` is a special tag, a placeholder that will be replaced with your page rendering.  
It's one of the very few "unholy" things Gracile is doing.  
However it is not meant as just an esthetic thing, compared to a more traditional comment marker (e.g., `<!--SSR_OUTLET-->`).  
Defined as a Custom Element, it might serve as a smart fallback or an ultimate error boundary in the future.
E.g., if the rendering fail, it could be used to show your own error overlay. It
can be an interesting pattern to explore.

`<slot></slot>` could have been used to, but it's a bit of a stretch of what a _real_ slot is, and can clash with legitimate slots.

## Declarative shadow DOM support

<caniuse-embed feature="declarative-shadow-dom" periods="future_1,current,past_1,past_2"></caniuse-embed>

<div class="git-only">

[**Declarative Shadow DOM** on caniuse.com](https://caniuse.com/declarative-shadow-dom)

</div>

It's pretty good!

But if you have to support older browsers, you can add a polyfill in a pinch like this:

```ts twoslash
// @filename: /src/document.ts

import { html } from '@gracile/gracile/server-html';
import { helpers } from '@gracile/server/document';

export const document = () => html`
  <!doctype html>
  <html>
    <head>
      <!-- ... -->
      ${helpers.polyfills.declarativeShadowDom}
      <!-- ... -->
    </head>
    <!-- ... -->
    <body>
      <route-template-outlet></route-template-outlet>
    </body>
  </html>
`;
```

## Notes

Gracile is purposedly separating the HTML base document with the rest of the
page rendering.  
It is because, under the hood, it will render it beforehand and stream it to the
user, resulting in a faster response time.  
Usually there isn't too much work at this level, and without those premices, the
browser isn't kickstarting its work before receiving the real meat of the
markup, which can take longer to process streamed chunks.

Note that you'll benefit from looser error boundaries, too. When a sub-template
fails to render, you still get the valid markup, instead of a giant crash.

Also, base document holds critical assets and Vite's magic sauce for dev.
Hence an additional reason to process it alone, in the pre-pass.

Finally, we are using `@lit-labs/ssr` own `html` tagged template literal, which
is different from the `lit`'s one.  
In short: it can handle all the "special" HTML tags (`<script>`, `<title>`,…)
while being faster to process, due to its non-interactive nature.  
It's also 1:1 HTML!  
Lit "regular" templates can also be used without the "DSL-y" bits (`@event`,
`.property`…) if you want to go your way, like no JS, vanilla JS, Alpine.js, etc.

You'll find that the "Lit HTML" abstraction, overall, is tiny, and opt-in for
the "special" stuff, like `@event`, property `.binding`, so you might just use it whenever you can.

---

It's better to separate the document from your route; because each of the Lit and Lit SSR, server-only `html` tags will clash otherwise.  
It's possible to rename it through import though, like `server.html`,
but you will probably lose formatting, syntax highlighting, TypeScript Lit Plugin hover info, etc.

Keep in mind you can use conditional logic inside your base document, so you can
alter metadata, assets loading etc. to cater to each route.

Most projects won't need to define multiple documents, but if you find yourself
having too many divergences, or for other reasons like security, you have the
flexibility to do that.

Gracile isn't aware of anything other than you route files, you can locate and name documents, components, etc. with your conventions.
