# @qitqode/plugin

Type definitions for the QitQode plugin API.

## This package is not on npm

`@qitqode/plugin` is compiled into the QitQode CLI binary from source and is
deliberately never published to the registry — see
`packages/qitqode/script/build-audit/inventory.ts`, where it is classified as an
`internal` package. `npm install @qitqode/plugin` will 404, and nothing you can do
in a `.qitqode` directory will change that.

That matters for one reason only: **a plugin file must never import a runtime value
from `@qitqode/plugin`.** The import would resolve at author time in this repository
and fail at load time on a user's machine. Use `import type` and you are fine — types
are erased before the file is ever executed.

## Writing a plugin

Drop a `.ts` or `.js` file into `.qitqode/plugin/` and QitQode picks it up
automatically. A plugin exports a `server` function that returns a set of hooks:

```ts
import type { Plugin } from "@qitqode/plugin"

export const server: Plugin = async ({ client, directory, worktree, $ }) => ({
  event: async ({ event }) => {
    if (event.type === "session.idle") {
      // ...
    }
  },
})
```

Tools are plain objects, so you never need the `tool()` helper at runtime — it is an
identity function that exists purely to infer argument types:

```ts
import type { Plugin } from "@qitqode/plugin"
import { z } from "zod"

export const server: Plugin = async () => ({
  tool: {
    ping: {
      description: "Reply with pong",
      args: { message: z.string() },
      execute: async (args) => `pong: ${args.message}`,
    },
  },
})
```

## Getting editor completion

QitQode does not install anything into your `.qitqode` directory to give you types —
an earlier version tried, and because this package is unpublished the attempt failed
with a 404 warning on every single launch. Pick whichever of these suits you:

**Nothing at all.** Plugins are duck-typed and are never type-checked by the CLI. An
untyped `.qitqode/plugin/my-plugin.js` works exactly as well as a typed one.

**Point TypeScript at a checkout.** Clone the repository once and map the package in
a `tsconfig.json` beside your plugin:

```jsonc
// .qitqode/tsconfig.json
{
  "compilerOptions": {
    "module": "preserve",
    "moduleResolution": "bundler",
    "paths": {
      "@qitqode/plugin": ["../../qitqode/packages/plugin/src/index.ts"],
      "@qitqode/plugin/*": ["../../qitqode/packages/plugin/src/*.ts"]
    }
  }
}
```

**Develop inside this repository.** Plugins written in the workspace resolve
`@qitqode/plugin` through pnpm with no extra setup.

If you declare your own dependencies in a `.qitqode/package.json`, QitQode installs
them for you on startup and reports it if that install fails.
