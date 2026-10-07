# QitQode VS Code Extension

A Visual Studio Code extension that integrates [QitQode](https://qitqode.com) directly into your development workflow.

## Prerequisites

This extension requires the QitQode CLI to be installed on your system.

```sh
npm install -g @qitqode/cli
```

## Features

- **Quick Launch**: Use `Cmd+Esc` (Mac) or `Ctrl+Esc` (Windows/Linux) to open QitQode in a split terminal view, or focus an existing session if one is already running.
- **New Session**: Use `Cmd+Shift+Esc` (Mac) or `Ctrl+Shift+Esc` (Windows/Linux) to start a new QitQode session, even if one is already open. You can also click the QitQode button in the editor toolbar.
- **Context Awareness**: Automatically shares your current file or selection with QitQode when a session opens.
- **File Reference Shortcuts**: Use `Cmd+Option+K` (Mac) or `Ctrl+Alt+K` (Windows/Linux) to insert a file reference into the active QitQode terminal. For example, `@src/index.ts#L37-42`.

## Support

If you encounter issues or have feedback, please open an issue at https://github.com/qitqode/qitqode/issues.

## Development

1. `code sdks/vscode` — Open the `sdks/vscode` directory in VS Code. **Do not open from the repo root.**
2. `bun install` — Run inside the `sdks/vscode` directory.
3. Press `F5` to start debugging — launches a new VS Code window with the extension loaded.

#### Making Changes

`tsc` and `esbuild` watchers run automatically during debugging (visible in the Terminal tab). Changes are rebuilt in the background.

To test your changes:

1. In the debug VS Code window press `Cmd+Shift+P`
2. Search for `Developer: Reload Window`
3. Reload to pick up the latest build without restarting the debug session
