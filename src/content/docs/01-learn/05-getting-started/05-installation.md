# 📦  Installation

## Requirements

- Node 20+

## Packages

```sh
npm install @gracile/gracile

# Addons…
npm install \
@gracile/metadata \
@gracile/prefetch \
@gracile/svg \
@gracile/markdown
# ...
```

See [add-ons](http://localhost:9898/docs/add-ons/).

---

Lit's dependencies.

```sh
npm install lit @lit-labs/ssr
```

### `package.json`

```json
{
  "name": "my-gracile-project",
  "type": "module",
  "private": true,

  "scripts": {
    "dev": "gracile dev",
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

### TypeScript `tsconfig.json`

```jsonc
{
  "compilerOptions": {
    "target": "ESNext",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],

    "module": "Preserve",

    // Lit
    "useDefineForClassFields": false,
    "experimentalDecorators": true,

    "checkJs": true,

    "strict": true,
    "noEmit": true,

    "plugins": [
      // TIP: Uncomment if you're not using the VSCode extension (e.g. with Zed, Neovim…)
      // { "name": "ts-lit-plugin" },
    ],
  },

  "include": ["./gracile.config.js", "./src/**/*"],
}
```

> [!IMPORTANT]
> Once Gracile become stable, core packages will be re-united into the **`gracile`** package (without the @ scope).
