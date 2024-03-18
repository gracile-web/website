# Passing data from server to client

## Lit Custom Element example

```ts twoslash
// @filename: /src/my-partial.ts

import { html } from 'lit';
import type { MyElement } from './my-element.ts';

const data = { foo: 'bar' } satisfies MyElement['initialData'];

export const myServerRenderedPartial = html`
  <!-- ... -->
  <section>
    <my-element initialData=${JSON.stringify(data)}></my-element>
  </section>
  <!-- ... -->
`;

// @filename: /src/my-element.ts

import { LitElement, css, html } from 'lit';
import { customElement, property } from 'lit/decorators.js';

@customElement('my-element')
export class MyElement extends LitElement {
  @property({ type: Object })
  initialData: {
    foo?: string;
  } = {};

  render() {
    console.log({ initial: this.initialData });
    return html`
      <!-- -->
      <div>${this.initialData.foo}</div>
    `;
  }
}
```

## Advanced serialization / deserialization

If you want to pass more complex data like object with self referencing children,
dates, and more, you can use a dedicated library for that, there are plenty.

For example the excellent [Seroval](https://github.com/lxsmnsyc/seroval) library can be used in conjunction with a Lit [`@property` custom converter](https://lit.dev/docs/components/properties/#conversion-converter) to properly "revive" the serialized data.

This is a fully working example:

```ts twoslash
// @filename: /src/document.ts
import { html } from '@lit-labs/ssr';

export const defaultDocument = (options: { url: URL }) => html`...`;
//---cut---

// @filename: /src/routes/serialization.ts

import { html } from 'lit';
import { defineRoute } from '@gracile/server/route';
import { defaultDocument } from '../document.js';
import { serialize } from 'seroval';
import { complexData } from './_serialization-demo-data.js';

export default defineRoute({
  document: defaultDocument,

  page: () => html`
    <script type="module" src="/src/routes/serialization.client.ts"></script>

    <serialization-example
      myComplexData=${serialize(complexData)}
    ></serialization-example>
  `,
});

// @filename: /src/routes/_serialization-demo-data.ts

export const complexData = {
  number: [Math.random(), -0, NaN, Infinity, -Infinity],
  string: ['hello world', '<script>Hello World</script>'],
  boolean: [true, false],
  null: null,
  undefined: undefined,
  bigint: 9007199254740991n,
  array: [, , ,], // holes
  regexp: /[a-z0-9]+/i,
  date: new Date(),
  map: new Map([['hello', 'world']]),
  set: new Set(['hello', 'world']),
} as const;

export type MyData = typeof complexData;

// self cyclic references
// recursive objects
complexData.self = complexData;
// recursive arrays
complexData.array.push(complexData.array);
// recursive maps
complexData.map.set('self', complexData.map);
// recursive sets
complexData.set.add(complexData.set);

// mutual cyclic references
complexData.array.push(complexData.map);
complexData.map.set('mutual', complexData.set);
complexData.set.add(complexData.array);

// @filename: /src/routes/serialization.client.ts

import { LitElement, html } from 'lit';
import { customElement, property } from 'lit/decorators.js';
import { serialize, deserialize } from 'seroval';

import type { MyData } from './_serialization-demo-data.js';

@customElement('serialization-example')
export class SerializationExample extends LitElement {
  @property({
    converter: {
      fromAttribute: (value) =>
        value ? deserialize<MyData>(value) : undefined,
    },
  })

  // NOTE: `string` is used to prevent a type error in the template. Maybe a more elegant solution can be achieved.
  myComplexData?: MyData | string;

  render() {
    if (typeof this.myComplexData !== 'object') return html`Invalid data`;

    return html`
      <span>Dump</span>
      <pre>${serialize(this.myComplexData)}</pre>

      <!--  -->

      ${this.myComplexData.map.size}

      <!--  -->

      ${this.myComplexData.date.getFullYear()}

      <!--  -->

      ${this.myComplexData.regexp.test('Regexp test string')}
    `;
  }
}
```
