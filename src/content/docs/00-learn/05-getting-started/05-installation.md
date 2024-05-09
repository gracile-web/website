# 📦 Installation

Start developing with Gracile in less than 1 minute ⏳.  
You can either use the CLI, fetch a starter or start from scratch.

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

  -t, --template <string>        Choose a starter template.

  -i, --install-dependencies     Automatically install dependencies
                                 with your detected package manager.

  -g, --initialize-git           Initialize a git repository after
                                 setting up your project.

  -s, --use-previous-settings    Whether or not we should load previous
                                 settings.

  -r, --clear-previous-settings  Clear previously saved settings.

  -h, --help                     display help for command
```

### Requirements

- git 2.39+
- npm 10.5+ / pnpm 9.0+

## Fetch a starter project

This an alternative way to create a preconfigured project with Gracile.

Shallow clone a starter project from the repository with the `degit` command.

```sh
npx degit gracile-web/starter-projects/minimal-static my-project

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
  "private": true,

  "scripts": {
    "dev": "gracile-dev",
    "build": "gracile build",
    "preview": "gracile preview"
  },

  "dependencies": {
    "@gracile/gracile": "latest"
  },
  "devDependencies": {
    "typescript": "latest"
  }
}
```

### Packages

```sh
npm install @gracile/gracile

# Addons…
npm install \
@gracile/metadata \
@gracile/svg \
@gracile/sitemap \
@gracile/markdown
# ...
```

<!-- @gracile/prefetch \ -->

See [add-ons](/docs/add-ons/).

#### Lit (optional)

```sh
npm install lit
```

> [!NOTE]  
> `lit` exports the `html` template literal which can be used
> for both **client** and **server**.  
> However, for a fully static website, the **server-only** `html` is enough.  
> It's the `@lit-labs/ssr`'s `html` which is re-exported from `@gracile/gracile/server-html`.

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

## Requirements

- Node v18.17+

---

> [!NOTE]
> Once Gracile become stable (reaching v1), the Gracile main package (`@gracile/gracile`) will be transferred to the **`gracile`** package (without the @ scope).
