# QitQode CLI

QitQode is a terminal-native AI coding agent distributed as a compiled Bun executable.

## Install

Install [Bun 1.3.11 or newer](https://bun.sh), then run:

```bash
bun add --global @qitqode/cli
qitqode
```

The standard package supports glibc Linux, macOS, and Windows on x64 or arm64.
On Alpine or another musl Linux distribution, install the runtime libraries and
the dedicated musl wrapper instead:

```bash
apk add libstdc++ libgcc
bun add --global @qitqode/cli-musl
qitqode
```

The dedicated wrapper prevents Bun from downloading both glibc and musl
binaries. The x64 builds use the baseline instruction set for compatibility.

`@qitqode/cli` and `@qitqode/cli-musl` are command-line applications, not Node, Bun, browser, ESM, or CommonJS library APIs.
Programmatic integrations should use `@qitqode/sdk` or `@qitqode/plugin`. Both are compiled into the CLI from source
rather than published to npm, so they cannot be installed from the registry. To extend QitQode, drop a `.ts` file into
`.qitqode/plugin/` and import from `@qitqode/plugin` with `import type` only — see
[packages/plugin/README.md](https://github.com/qitqode/qitqode/blob/main/packages/plugin/README.md) for the hook API and
for how to wire up editor completion.

Automatic language-server downloads are currently disabled. Install the language servers you need through your
operating system or language package manager. Update checks are notification-only unless `autoupdate` is explicitly
set to `true`.

At runtime, QitQode sends prompt context to the selected QitQode/model backend. It also retrieves the public
`models.dev` catalog and package-version metadata. Remote product analytics are currently hard-disabled.
Operator-configured OpenTelemetry remains available only when `OTEL_EXPORTER_OTLP_ENDPOINT` is explicitly set.

Source and documentation are available in the
[QitQode repository](https://github.com/qitqode/qitqode).
