# Markdown

Import markdown file directly in HTML templates with your own processing
pipeline.  
Extracts the table of content, frontmatter, excerpt and title.

> [!WARNING]
> This section is under construction.

## Installation

```sh
npm i @gracile/markdown
```

```ts twoslash
// @filename: /gracile.config.ts

import { defineConfig } from '@gracile/gracile';

import { viteMarkdownPlugin } from '@gracile/markdown/addon'; // [!code highlight]

export default defineConfig({
  // ...

  vite: {
    plugins: [
      viteMarkdownPlugin(),
      // ...
    ],
  },
});
```

## Usage

```ts twoslash
// @filename: /src/modules/my-partial.ts

import { html } from 'lit';

import * as myDocument from './my-document.md' with { type: 'md-lit' };

export const myPartial = html`
  <article>
    <h1>${myDocument.title}</h1>
    <div class="content">${myDocument.content}</div>
  </article>

  <details>
    <div>${myDocument.excerpt}</div>
    <pre>${JSON.stringify(myDocument.toc)}</pre>
    <pre>${JSON.stringify(myDocument.frontmatter)}</pre>
  </details>
`;
```

### With Vite's glob import.

```ts twoslash
import type { MarkdownModule } from '@gracile/markdown/md-module';

const docsImportsGlob = import.meta.glob('/src/content/docs/**/*.md', {
  eager: true,
});
```
