# Home

<section class="cards">
<div class="card">
<div class="card-content">

## Ease of use

Write the same markup, styling and scripting languages for both server and client side.  
The ones that you already know and use everywhere else: **HTML**, **CSS** and **JavaScript**.

</div>
</div>
<div class="card">
<div class="card-content">

## Standards oriented

Built with a platform-minded philosophy. Every time a standard can be leveraged for a task, it should be.  
It also means fewer vendor-specific idioms to churn on and a more portable codebase overall.  
Stop re-implementing the wheel, and embrace **future-proof APIs**, you'll thank yourself later!

</div>
</div>

<div class="card">
<div class="card-content">

## Developer Experience

The DX bar has been constantly raised, alongside developers' expectations about their everyday tooling.  
The "Vanilla" community is full of gems, in a scattered way.  
Gracile provides an integrated, **out-of-the-box** experience while keeping
non-core opinions as _opt-ins_.

</div>
</div>

<div class="card">
<div class="card-content">

## Convention over configuration

Finding the right balance between **convenience** and **freedom** is tricky.  
Hopefully, more and more patterns will be established in the full-stack JS space.

Gracile is inspired by those widespread practices that will
make you feel at home.

</div>
</div>

<div class="card">
<div class="card-content">

## Performances

**Speed is not the main goal** for Gracile,
that's because it is just the sane default you'll start with.  
Avoiding complex template transformations, or surgically shipping client-side JS are just a few facets of what makes Gracile a "_do more with less_" power tool.

</div>
</div>

</section>

# Annotated example

<section class="code-example">

<div>

> [!TIP]
> You can hover source code tokens, like in your local editor, to get more insights.

</div>

```ts twoslash
// @filename: /src/lib/db.ts

/** Dummy DB handler… */
export const db = {
  query<T>(str: string) {
    return {} as T[];
  },
};

export type Achievement = { name: string; date: Date };

/** Dummy SQL template literal… */
export function sql(str: string) {
  return '';
}
// ---cut---

// @filename: /src/document.ts

import { html } from '@lit-labs/ssr';
import { helpers } from '@gracile/server/document';

export const defaultDocument = (options: { url: URL; title?: string }) => html`
  <!doctype html>
  <html
    lang="en"
    class=${`page${options.url.pathname.replace('/', '-') || 'home'}`}
  >
    <head>
      <!-- NOTE: Helpers -->
      ${helpers.fullHydration}

      <!-- NOTE: Global assets -->
      <link rel="stylesheet" href="/src/styles/global.scss" />
      <script type="module" src="/src/document.client.ts"></script>

      <!-- NOTE: SEO -->
      <title>${options.title ?? 'My Website'}</title>

      <!-- ... -->
    </head>

    <body data-pagefind-body>
      <!-- IMPORTANT: Current route's page injection -->
      <page-outlet></page-outlet>
    </body>
  </html>
`;

// @filename: /src/routes/index.ts

import { html } from 'lit';
import { defineRoute } from '@gracile/server/route';

import { db, sql, Achievement } from '../lib/db.js';

import { defaultDocument } from '../document.js';

import * as homeReadme from '../content/README.md' with { type: 'markdown-lit' };

import type { MyElement } from '../components/my-element.ts';
import '../components/my-element.js';

export default defineRoute({
  handler: {
    POST: async (context) => {
      const formData = await context.request.formData();
      const name = formData.get('achievement')?.toString();

      if (name) {
        await db.query(sql`INSERT INTO achievements (name); VALUES (${name})`);

        return Response.redirect(context.url, 303);
      }

      context.response.status = 400;
      context.response.statusText = 'Wrong input!';
      return { success: false, message: context.response.statusText };
    },
  },

  document: (context) =>
    defaultDocument({ url: context.url, title: homeReadme.title }),

  page: async (context) => {
    const initialData = { foo: 'bar' } satisfies MyElement['initialData'];

    const achievements = await db.query<Achievement>(
      sql`SELECT * FROM achievements`,
    );

    return html`
      <!-- NOTE: Bundles entrypoints for the current page assets -->
      <link rel="stylesheet" href="/src/routes/index.scss" />
      <script type="module" src="/src/routes/index.client.ts"></script>
      <!-- NOTE: You can also use inline (deferred) modules or in-path scripts… -->
      <script type="module">
        await new Promise((r) => setTimeout(() => r(console.log('Hi!')), 1500));
      </script>

      <h1>${homeReadme.title}</h1>

      <main>
        <article>${homeReadme.content}</article>
      </main>

      <aside>
        <form method="post">
          <input type="text" name="achievement" />
          <button>Add achievement</button>

          <span>${context.props.POST?.message}</span>
        </form>

        <my-element initialData=${JSON.stringify(initialData)}></my-element>
        <my-client-only-element></my-client-only-element>

        <footer>
          ${achievements.map(
            (achievement) =>
              html`<section class=${`achievement-${achievement.name}`}>
                <h1>${achievement.name}</h1>
                <p>${achievement.date}</p>
              </section>`,
          )}
        </footer>
      </aside>

      <footer>
        <small>
          You are visiting ${context.url.href},
          <!-- -->
          with method ${context.request.method}.
        </small>
      </footer>
    `;
  },
});

// @filename: /src/routes/index.client.ts

// NOTE: Importing your components in this page's client bundle entrypoint will make the server markup alive.

requestIdleCallback(() => import('../components/my-element.js'));
// ...

// TIP: Don't import on server-side, if you want a client-only element.
import '../components/my-client-only-element.js';

console.log('Welcome', navigator.userAgent);
// ...

// @filename: /src/components/my-element.ts

import { LitElement, css, html } from 'lit';
import { customElement, property } from 'lit/decorators.js';
import { styleMap } from 'lit/directives/style-map.js';

@customElement('my-element')
export class MyElement extends LitElement {
  static readonly GREETING = 'Hello';

  @property({ type: Object }) initialData: { foo?: string } = {};

  @property({ type: Number }) bgTint = 0.5;

  render() {
    return html`
      <div
        @click=${() => (this.bgTint = Math.random())}
        style=${styleMap({ '--bg-tint': this.bgTint })}
      >
        ${this.initialData.foo} - ${MyElement.GREETING}
      </div>
    `;
  }

  static styles = [
    css`
      :host {
        display: block;
        margin: 1rem;
      }

      div {
        background: hsl(calc(var(--bg-tint, 0) * 360), 50%, 50%);
      }
    `,
  ];
}
```

</section>

<div>

# FAQ

<section class="cards">

<div class="card">
<div class="card-content">

## Why?

There wasn't much (if any?) platform-oriented, **full-stack** meta-framework yet.  
Rocket, Eleventy and Astro: all have great approaches on how to server render
Web Components (they all use Lit-SSR under the hood).
However, Rocket and Eleventy
are _content-oriented_ (static output) and Astro has a JSX processor/template
compiler that you might not want to minimize layers.  
JSX _looks_ like HTML, but that also means you will still be tied to the React
world (TS JSX namespace, IDE language tools…).

While that appears antagonistic toward the decentralized ethos of web platform enthusiasts, there is room for
consolidation and reference implementations, especially on the server side.

The base goal is to make the plunge easier for beginners
but also to bring comfortable bootstraps for accustomed developers.

</div>
</div>

<div class="card">
<div class="card-content">

## Is it tied to any vendor?

Build-less, vendor-free, etc. are all reasonable goals to aim for.  
Fewer tools generally mean fewer maintenance issues.

However, we didn't reach those yet, that's why we still need to use some tooling
to fill the gaps.  
Also, it is perfectly reasonable to use TypeScript or CSS preprocessors, because
for many, the gains largely outweigh the cost, and source-maps are here to help.  
In fact, this website is made with TS and SCSS. You don't have to throw
away your favorite language superset to enjoy the benefits of being
closer to the web platform!

Gracile relies heavily on Vite's ecosystem but also on Lit's SSR library, which,
like all Lit contributions, are forward-thinking intents for the web.  
Your server-side templates are just portable HTML, with JS-based composability,
safety and DX goodies underneath, meaning nearly zero lock-in.

Think of those as "blending" tools.  
They don't force you into unholy contortions.

</div>
</div>

<div class="card">
<div class="card-content">

## Do I need a specific server runtime?

Gracile's "server" mode build is just outputting a handler.  
From there, you're free to embed it in any standard-friendly HTTP server or use a `Request`/`Response` adapter.

As for static builds, you'll get conformed assets that can be deployed anywhere.

When developing, any JavaScript engine that supports Vite can be used.

Efforts are made to keep Gracile as runtime agnostic as possible, with the
leading runtime, Node, as the standard baseline and WinterCG proposals as a
compass. We're slowly getting there, but fragmentation remains.
That being said, you're still free to engage in side-ways, if that's your will!

</div>
</div>

<div class="card">
<div class="card-content">

## How does it compare to XYZ?

You'll find Gracile is inspired by Fresh, Astro, Elder.js, Nuxt, Rocket, Remix…
you name it!  
At one point, everyone is copying everyone when an idea is valuable.  
However, there are those things that make it a bit different:  
It's not centered around one or a few UI libraries, nor is it tied to
domain-specific languages or deep bundler-tied abstractions.

Every time a standard is going mainstream, that should be
the occasion to prune user-land stopgaps (the `node_modules` black-hole, browser pony-fills…).  
Also, expanding the scope of a framework outside core features has a cost: more
dependencies, maintenance and opinions.  
"_Scope creep_" is what makes you ultimately dependent on a framework.  
_Do-everything_ frameworks are cool (think Rails, Laravel…), but that's not
the goal of Gracile.
It's up to you to choose your data-sourcing strategy, UI stack,
HTTP server…

</div>
</div>

<div class="card">
<div class="card-content">

## Do I need to use Web Components / Shadow DOM?

Not at all!  
This whole website is mostly made with composed function calls
returning streamed, serialized light DOM, then rendered ahead of time.

Progressive enhancements can be achieved with good old, per-route or site-wide
inline JavaScript or modules.  
It's also possible to bring Alpine, HTMX, HTMZ, etc. if that's your jam!

When you need more interactivity and composition superpowers, or if you are already an avid Lit user you can jump
straight into Lit flavored Web Components. They are the only ones that are server renderable
and client hydratable. That's because of their intrinsic template serialization
capabilities.  
Lit's renderer is stitching template strings on the client and server,
and guess what, that's already a perfect base for server rendering!  
Strings concats are the inevitable approach used by every SSR engine (Astro, SolidStart, Next.js…).

Conceivably, at some point, we'll get "Vanilla" HTML Custom Elements to be SSR'ed
canonically.

</div>
</div>

<div class="card">
<div class="card-content">

## What is the current state of this project?

It's experimental.  
Lately, things are changing **very** fast in the web components or
platform-related spaces.  
Declarative Shadow DOM has just rooted in every major browser, Constructible StyleSheets
and CSS Modules are following the same path… and that's a few "glaring" stuff.

This project is an attempt to normalize using the web platform amongst
developer who are more familiar with "branded" UI libraries and their respective
meta-frameworks (think React, Vue, Svelte, Solid…).  
We are getting there, but what we often hear is Web Components and other modern native APIs
are "hidden gems" that need to be polished up, and from there, start to shine.  
This awareness is better demonstrated with a cohesive experience, for the best impact.  
Hopefully, big names will start to invest in the framework space, as they started with WC design systems.  
More competition will make this ecosystem flourish even more.

</div>
</div>

</section>

</div>
