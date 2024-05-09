# 🖥️ Command-line interface

This are all the commands you need to interact with the Gracile engine locally.

## Commands

```sh
gracile-dev # Note the hyphen

gracile build

gracile preview
```

You can alias these in `package.json` scripts:

```json
{
  "scripts": {
    "dev": "gracile-dev",
    "build": "gracile build",
    "preview": "gracile preview"
  }
}
```

See also the ["Create" command](/docs/learn/getting-started/installation/#doc_the-create-command) (`npm create gracile`) for bootstrapping a project in under a minute.

## Options

```text
  -p, --port <number>  Assign a local port (overrides config. file)
  -h, --host           Expose the server to you local network (0.0.0.0)
  --help               display help for command
```
