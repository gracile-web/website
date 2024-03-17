# Prefetching

Links prefetching is a simple but powerful technique to make page changes
much snappier.
Add a data attribute on a link, then it will be collected to trigger
a document prefetch on hover.

You can actually inspect this behavior on this current page.  
Search for the `<link>` with `data-prefetch-document` on it,
hover some links in the tree menu and see what happens.

> [!WARNING]
> This section is under construction.

## Installation

```sh
npm i @gracile/prefetch
```

## Usage

**Client side** helper.

```ts twoslash
import { Prefetcher } from '@gracile/prefetch';

const prefetcher = new Prefetcher();
prefetcher.collectLinks();
```

---

```html
<a data-prefetch href="/my-link/">Prefetched link</a>
```

Will prefetch the link on **hover**.
