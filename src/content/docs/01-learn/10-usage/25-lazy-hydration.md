# Lazy component hydration (Islands)

It's not stricly speaking "Islands", as you're mostly using native platform
capabilities.  
However those patterns share the same goal: loading JavaScript and hydrate the
DOM in a lazy fashion.

## Loading when the browser is idle

As an illustration, the very docs website you are visiting right now has a search bar which is made of numerous JS-heavy modules.  
There is the Pagefind engine UI, Pagefind index chunks (with WASM), the Shoelace dialog component, the `<search-popup>` custom element itself, the CSS…

All this doesn't need to be loaded eagerly, this is why you can defer these tasks when
everything else is settled.

Hopefully, you got the native `requestIdleCallback` global function just for accomplishing this!

Combined with lazy ES Module (`import(…)`), Vite / Rollup smart bundling, Custom Elements registry, and Lit's auto-hydration, you'll get a full fledged "Island" pattern to work with.

First we got this template partial. When loading this payload in the browser, everything is static (for now):

```ts twoslash
// @filename: /src/modules/site-search.ts

import { html } from 'lit';

export function siteSearch() {
  return html`
    <script type="module" src="/src/modules/site-search.client.ts"></script>

    <div class="m-site-search">
      <!-- v—— Not loaded yet! It is just a generic element for now -->
      <search-popup>
        <button>Search</button>
      </search-popup>
    </div>
  `;
}
```

Our `<search-popup>` component can look like this:

```ts twoslash
// @filename: /src/components/search-popup.ts

import { LitElement, html } from 'lit';
import { customElement } from 'lit/decorators.js';
// ...
// @ts-ignore This will be loaded lazily alongside the web component.
await import('/my-HEAVY-stuff.js');

@customElement('search-popup')
export class SearchPopup extends LitElement {
  render() {
    return html`
      <!-- The button in light DOM will be slotted here -->
      <slot></slot>

      <sl-dialog>
        <!-- ... -->
      </sl-dialog>
    `;
  }
}
```

And now, the magic trick is:

```ts twoslash
// @filename: /src/modules/site-search.client.ts

requestIdleCallback(() => import('../components/search-popup.js'));
```

That's all you need for deferring non-visible components when your user browser has finished doing critical work on page load.  
Note that for everything visible right away, you should avoid this technique
or it will cause a flash of unstyled content (FOUC).

## Load on user interaction

Do the same as the first example above, but instead of using `requestIdleCallback` in
the client script, you could do something like:

```ts twoslash
// @filename: /src/modules/site-search.client.ts

// NOTE: It will be imported once in the module graph, anyway, so this check is optional.
let searchPopupLoaded = false;

document.querySelector('search-popup').addEventListener('mouseenter', () => {
  if (searchPopupLoaded) return;

  import('../components/search-popup.js');
  searchPopupLoaded = true;
});
```

You have total control on how you want to selectively "hydrate".  
You can
use any kind of interaction, with `IntersectionObserver`, when clicking specific elements, etc.

You could also generalize you preferred approaches with a declarative pattern, like adding `data-load-on="idle|hover|visible|..."` on the elements to target.
