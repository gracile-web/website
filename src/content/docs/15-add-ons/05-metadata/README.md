# Page metadata (head)

Quickly add a description, title, open graph, etc. in your page's document head.  
This add-on will take care of the nitty-gritty.

> [!WARNING]
> This section is under construction.

## Installation

> [!TIP]
> You can u**se this extension with any Vite+Lit SSR setup**!  
> It's totally decoupled from the framework.

```sh
npm i @gracile/metadata
```

## Usage

```ts twoslash
// @filename: /src/document.ts

import { html } from '@gracile/gracile/server-html';
import { helpers } from '@gracile/gracile/document';
import { createMetadata } from '@gracile/metadata';

export const document = (props: { url: URL; title?: string }) => html`
  <!doctype html>
  <html lang="en">
    <head>
      <!-- SEO -->
      ${createMetadata({
        siteTitle: 'My Site',
        pageTitle: `My Page | ${props.title ?? 'Untitled'}`,
        ogTitle: props.title,
        pageDescription: 'The description…',
        // ...
      })}
    </head>

    <body>
      <route-template-outlet></route-template-outlet>
    </body>
  </html>
`;
```

Yields (roughly):

```html
<head>
  <!-- ... -->

  <!-- SEO -->

  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />

  <link href="http://localhost:9898/" rel="canonical" />
  <meta property="og:url" content="http://localhost:9898/" />
  <meta name="twitter:url" content="http://localhost:9898/" />

  <meta property="og:type" content="website" />

  <meta property="og:locale" content="en_US" />

  <meta name="apple-mobile-web-app-title" content="Gracile" />
  <meta property="og:site_name" content="Gracile" />

  <title>Gracile | Page metadata (head)</title>
  <meta property="og:title" content="Page metadata (head)" />
  <meta name="twitter:title" content="Page metadata (head)" />

  <meta
    property="og:image:alt"
    content="Quickly add a description, title,
open graph, etc. in your page's document head.
This add-on will take care of the nitty-gritty."
  />
  <meta
    name="description"
    property="og:description"
    content="Quickly add a description, title,
open graph, etc. in your page's document head.
This add-on will take care of the nitty-gritty."
  />
  <meta
    name="twitter:description"
    content="Quickly add a description, title,
open graph, etc. in your page's document head.
This add-on will take care of the nitty-gritty."
  />

  <meta property="og:image" content="/og/docs/add-ons/metadata.png" />
  <meta name="twitter:image:src" content="/og/docs/add-ons/metadata.png" />
  <meta name="twitter:card" content="summary_large_image" />
  <meta property="og:image:width" content="1200" />
  <meta property="og:image:height" content="630" />

  <meta name="author" content="Julian Cataldo" />

  <meta name="copyright" content="ISC" />
  <meta name="generator" content="Gracile v0-Alpha" />

  <link type="image/svg+xml" href="/favicon.svg" rel="icon" />

  <link type="application/xml" href="/sitemap.xml" rel="sitemap" />

  <!-- ... -->
</head>
```

Yes… all that!
