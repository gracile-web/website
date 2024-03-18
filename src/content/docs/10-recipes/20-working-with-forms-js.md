# Working with forms (JS augmented)

This is a full example of how to handle forms in Gracile, with client-side JavaScript augmentation.

Both methods will work, so the user can start submitting the form even if
JavaScript isn't parsed yet! And if it is, that will avoid a full-page reload.

```ts twoslash
// @filename: /src/document.ts
import { html } from '@lit-labs/ssr';
export const defaultDocument = (options: { url: URL }) => html` ... `;

// ---cut---
// @filename: /src/routes/form-js.client.ts

customElements.define(
  'form-augmented',
  class extends HTMLElement {
    #form = this.querySelector('form')!;
    #debugger = this.querySelector('pre#debug')!;

    connectedCallback() {
      this.#form.addEventListener('submit', (event) => {
        event.preventDefault();

        // NOTE: This will re-emit a "formdata" event.
        new FormData(this.#form);
      });

      this.#form.addEventListener('formdata', (event) =>
        this.post(event.formData),
      );
    }

    async post(formData: FormData) {
      // TIP: Inform the server to respond with JSON instead of doing a POST/Redirect/GET.
      formData.set('format', 'json');

      const result = await fetch('', { method: 'POST', body: formData }).then(
        (r) => r.json(),
      );

      // NOTE: Do stuff with the result without doing a full page reload…
      console.log({ result });

      this.#debugger.innerHTML = JSON.stringify(result, null, 2);
    }
  },
);

// @filename: /src/routes/form.ts

import { html } from 'lit';
import { defineRoute, jsonResponse } from '@gracile/server/route';
import { defaultDocument } from '../document.js';

let myData = 'untouched';

export default defineRoute({
  handler: {
    POST: async (context) => {
      const formData = await context.request.formData();

      const props = { success: false, myData };

      const myFieldValue = formData.get('my_field')?.toString();
      if (!myFieldValue) {
        context.response.status = 400;
        context.response.statusText = 'Missing field.';
      } else {
        props.success = true;
        myData = myFieldValue;
      }

      if (formData.get('format') === 'json') {
        return jsonResponse(props, context.response);
      } else {
        // TIP: No-JS fallback
        if (props.success) {
          return Response.redirect(context.url, 303);
        } else {
          return props;
        }
      }
    },
  },

  document: (context) => defaultDocument(context),

  page: (context) => {
    console.log(context);

    return html`
      <script type="module" src="/src/routes/form-js.client.ts"></script>

      <code>${context.request.method}</code>

      <form-augmented>
        <form method="post">
          <input type="text" value=${myData} name="my_field" />
          <button>Change field value</button>
        </form>

        <pre id="debug">${context.props.POST?.success} - ${myData}</pre>
      </form-augmented>
    `;
  },
});
```
