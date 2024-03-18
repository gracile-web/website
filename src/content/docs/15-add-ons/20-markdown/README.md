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

### Bringing your processing pipeline

> [!WARNING]
> This section is under construction.

The API will be designed to allow your Markdown pipeline to be used
by the **bundler** (Markdown "modules") and/or within your app-runtime, for
dynamic server transformation.

### With Vite's glob import.

```ts twoslash
import type { MarkdownModule } from '@gracile/markdown/md-module';

const docsImportsGlob = import.meta.glob('/src/content/docs/**/*.md', {
  eager: true,
});
```

## Server-side rendering

As with any `lit-html` templates, you can use Web Components inside Markdown,
they will be **server-side-rendered** and **hydrated** (if needed) once
inside the client!

As an example, inspect this icon with your browser dev tools:

<div align="center">
<gr-icon name="link" style="--gr-icon-width: 2rem; --gr-icon-height: 2rem;"></gr-icon>
</div>

### Opinions

> [!NOTE]
> Will speak as a framework author here.

Server rendering custom HTML elements is a **very** powerful pattern for
Markdown, arguably much more elegant than most "smart MD" implementations.  
They all share a major trait which is:

Not respecting the Markdown specs., which already provide full HTML support,
so why not leverage it?  
Web Components weren't a thing at the time Markdown was designed!

In practice, non-standardisms mean your Markdown will be riddled with machine
code when previewing inside your IDE or GitHub, which kind of defeats the
purpose of this ubiquitous, human-readable format.

Other implementations are:

- Custom **Mustache**-based syntaxes (think _Dev.to_' editor).
- **Markdoc** (powerful \[maybe too much\] but still non-standard).
- Markdown **directives** (not very elegant when un-renderable, while not adding much value
  over good old HTML)
- **MDX** (very cool for React-like Storybooks, but tied to JSX, and hard to debug).

MDX is a bit apart, it's a whole "wrapping" language,
not just an MD's pre-processed extension, like the three others.  
That means you could encounter discrepancies when using `unified` plugins.  
Again, this is fine if you know what you're doing, and if you are already
deep in the React world.  
You just have to be informed of those fundamental differences,
and their implications.

Let's do a quick comparison of apples and oranges, for fun
(this is untested code, from my memories, it might contain errors):

---

**Markdoc**

```md
{% city
   name="San Francisco"
   deleted=false
   coordinates=[1, 4, 9]
   meta={ id: "id_123" }
   color="red" /%}
```

No syntax highlight without special plugins.  
This code is rendered _as-is_ in unaware previewers.

---

**MDX**

```mdx
<City
  name="San Francisco"
  deleted={false}
  coordinates={[1, 4, 9]}
  meta={{ id: 'id_123' }}
  color="red"
/>
```

Better, but nothing is rendered without spinning
up a full JS runtime + processor. Plus it will not render the same with
different interpreters (to be fair, Web Component is the case, too).  
And what if you have a syntax error? Nothing will be shown at all.  
The parser or runtime will crash. Blank screen of death.  
On the other hand, HTML and Markdown are error-prone. There is no such
thing as "syntax error", just unexpectedly rendered pieces, at most.  
The browser is here to save us by gobbling up malformed HTML.  
MDX is predominantly Markdown for React/JSX devs, not for everyone.

I believe it's better to have a bit of incorrect content rather
than no content at all! We are not launching space ships, we just want
to communicate knowledge to humans effectively,
even if it's a tad quirky sometimes.

---

**Markdown directives**

```md
:::city{
name="San Francisco"
deleted=false
coordinates={[1, 4, 9]}
meta={{ id: "id_123" }}
color="red"
}
:::
```

Directives are a very interesting [CommonMark
proposal](https://talk.commonmark.org/t/generic-directives-plugins-syntax/444)
that never made it through.

You can get some syntax support with the "MDC" VSCode extension.  
Formatting is hazardous, plus this syntax can be confusing. It will be everywhere
in your GitHub repo., as-is, un-rendered in preview.  
In the end, it is just syntactic sugar, without that much sugar added IMHO.  
But this idea will keep floating around, so it has merits.

---

Now with pure **HTML in Markdown**:

```html
<city-viewer
  deleted="false"
  coordinates="[1, 4, 9]"
  meta='{ id: "id_123" }'
  color="red"
>
  <span slot="name">San Francisco</span>
</city-viewer>
```

Pretty syntax highlight AND formatting 😃!  
Yes, you will not have the smart, interactive component in "dumb" Markdown
previewers, but at least, you can build on top of REAL content that will
gracefully fall back and be displayed, like this basic `slot` example above.

As always with Web Components and HTML, debugging will also be easier, because,
well… you just have your component rendered out with a 1:1 correspondence. Also,
it will force you to keep your content as static as possible, which means easier
authoring in the long run.  
Most importantly, no new unusual syntax to learn and it is copy-pastable in your
regular HTML, back-and-forth!

If you really need to build a whole interactive UI with Markdown,
it is still doable, but it is not in the original spirit of this language.  
This is the beauty of it: like everything with Gracile, the goal is to build
on top of what the web is already offering us, not re-invent abstractions.

---

These opinions are debatable, but over time I (the author of Gracile) found
myself resorting to HTML when I wanted to "augment" my Markdown.

It will just disappear itself when using a bare MD reader.
And you can still show a fallback into the Web Component slots!  
You can also go quite far with just some sprinkles of CSS/JS.

I think those approaches are under-valued right now, in the
content-driven development spheres.  
I firmly believe that adding Turing-completeness, composability and other
wizz-bangs to something supposed to be beginner-friendly like Markdown
is not where we want to go. Technical writers are, well…, supposed to _write_,
not code programs to deliver content. They most likely know a bit of
HTML, rather than JSX or Markdoc, too.

---

**TL;DR**: Keep stuff simple and stupid.
**Standard, declarative and portable**.

<small>Please note that this is not a thorough comparison, so you should
take this with a grain of salt.  
If you prefer other solutions, it's fine!  
Also don't forget the immense `unified` ecosystem, which can itself help to pre-process
stuff a lot.
</small>

### Limitations

For now, Lit SSR only renders Web Components with Declarative Shadow Root.  
That might throw you off if you're a die-hard Light DOM adept. It's
understandable in the context of Markdown rendering where you want to
style everything globally. Indeed, you can take advantage of the Cascade of CSS,
a perfect use case for the art of typography.

There is some hope though:

[Some discussion](https://github.com/lit/lit/issues/3080) about
supporting Light-DOM rendering via Lit's' `createRenderRoot` is happening.  
And [open-stylable](https://github.com/WICG/webcomponents/issues/909) shadow
root is attracting a lot of interest from platform-minded developers.

For now, you can leverage the Shadow Root for what is it good for: isolation.  
Components like icons, geographical maps, etc. are very well suited for that,
because you don't want your typography styles to leak in them anyway.
