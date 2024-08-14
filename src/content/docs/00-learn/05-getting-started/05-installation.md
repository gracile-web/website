# 📦 Installation

Start developing with Gracile in less than 1 minute ⏳.  
You can either use the CLI, a starter template, convert an existing Vite project, or start from scratch.

## The "Create" command

This is the quickest way to create a preconfigured project with Gracile.

```sh
npm create gracile@latest
pnpm create gracile@latest
bun create gracile@latest
yarn create gracile@latest
```

<!-- TODO: make a macro component -->
<asciinema-player href="/assets/create-gracile.cast" loop autoplay speed="1.3334" theme="gracile" terminalFontFamily="'Fira Code', monospace">
<asciinema-player-header slot="header">create gracile</asciinema-player-header>
</asciinema-player>

### Options

```text
  -d, --location <string>        Project directory location.

  -t, --template <string>        Choose a starter template. Available:
                                 minimal-static, minimal-server-express,
                                 minimal-server-hono, basics-blog-static,
                                 basics-server

  -n, --next                     Use the `next` version of the selected template.

  -i, --install-dependencies     Automatically install dependencies with your detected
                                 package manager.

  -g, --initialize-git           Initialize a git repository after setting up your project.

  -s, --use-previous-settings    Whether or not we should load previous settings.

  -r, --clear-previous-settings  Clear previously saved settings.

  -h, --help                     display help for command
```

`npm create gracile@next` can be used to try the pre-release version of the `create-gracile`, CLI, too.

### Requirements

- git 2.39+
- npm 10.5+ / pnpm 9.0+

## Fetch a starter project

This is an alternative way to create a preconfigured project with Gracile.

Shallow clone a starter project from the repository with the `degit` command.

```sh
npx degit gracile-web/starter-projects/templates/minimal-static my-project

cd my-project
npm run dev
```

[See the starter list.](https://github.com/gracile-web/starter-projects).

## Install manually

Naturally, you can also start a project from scratch.  
Here is a minimal boilerplate.

### `package.json`

```json
{
  "name": "my-gracile-project",
  "type": "module",
  "version": "0.0.0",
  "private": true,

  "scripts": {
    "dev": "vite",
    "check": "tsc",
    "build": "vite build",
    "preview": "vite preview"
  },

  "dependencies": {
    "@gracile/gracile": "latest",
    "vite": "latest"
  },
  "devDependencies": {
    "@types/node": "latest",
    "typescript": "latest"
  }
}
```

For a server build, you can use these extra shorthands:

```jsonc
{
  "scripts": {
    // ...
    "preview": "node --watch -C preview server.js",
    "start": "node server.js",
    // TIP: Or with a TypeScript runner.
    "preview": "tsx --watch -C preview server.js",
    "start": "tsx server.js",
  },
}
```

The `-C` stands for `--conditions`.

You'll find that Gracile relies fairly
on [Node's export conditions](https://nodejs.org/api/packages.html#conditional-exports).  
Here, it's useful to get the logger running, while it's not in `production` (the default).

Those are just hints, it's up to you to integrate with your favorite workflow, like using `tsc` + `node --watch` instead of `tsx` etc.  
The server is running separately from Vite, it's totally outside the Gracile/Vite realms.

### Packages installation

```sh
npm install @gracile/gracile

# Next version, in development
npm install @gracile/gracile@next

# Addons…
npm install \
@gracile/metadata \
@gracile/svg \
@gracile/sitemap \
@gracile/markdown
# ...
```

<!-- @gracile/prefetch \ -->

See all [add-ons](/docs/add-ons/).

---

The `@next` tag can be used for any `@gracile/*` package.  
That way, you will get a preview of the **unreleased** builds.

#### Lit (optional)

```sh
npm install lit
```

> [!NOTE]  
> `lit` exports the `html` template literal which can be used
> for both **client** and **server**.  
> However, for a fully static website, the **server-only** `html` is enough.  
> It's the `@lit-labs/ssr`'s `html` which is re-exported from `@gracile/gracile/server-html`.

Lit exports some utilities that you'll find useful, even if you're not using
hydration and other client features at all.

<!-- Gracile is defining those as peer dependencies, pinned to major semver.
That means you can use the version you want in that range, without the
possibility of mismatching versions. -->

<!-- Choice has been made to not re-export `html`, etc. from Lit (e.g., in `context`)
because it's more explicit, modular and less error-prone that way. -->

### TypeScript `tsconfig.json` extendable presets

Three levels of type safety are available with these presets: `base`, `strict` or `strictest`.

```jsonc
{
  "extends": "@gracile/gracile/tsconfigs/strictest", // Or `base` | `strict`
}
```

### Ambient typings

```ts twoslash
// @filename: /ambient.d.ts

// NOTE: This will also forward Vite's own typings, like "import.meta.env" etc.
/// <reference types="@gracile/gracile/ambient" />

// For add-ons and third-parties…
/// <reference types="@gracile/markdown/ambient" />
/// <reference types="@gracile/svg/ambient" />
// ...
```

## Requirements

- Node v18.17+

---

> [!NOTE]
> Once Gracile become stable (reaching v1), the Gracile main package (`@gracile/gracile`) will be transferred to the **`gracile`** package (without the @ scope).
