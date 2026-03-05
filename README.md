# oxc-nested-git-repro

Minimal reproduction for [oxc-project/oxc-vscode#112](https://github.com/oxc-project/oxc-vscode/issues/112).

## Issue

The oxc VS Code extension LSP does not lint files inside subdirectories that are standalone git repos (have their own `.git/`).

## Structure

```
.git/                      ← root git repo
.oxlintrc.json             ← shared lint config
src/index.js               ← linted correctly ✅
packages/app-a/.git/       ← nested git repo
packages/app-a/src/index.js  ← NOT linted by LSP ❌
packages/app-b/.git/       ← nested git repo
packages/app-b/src/index.js  ← NOT linted by LSP ❌
```

## Steps to reproduce

1. Clone this repo
2. Open the root folder in VS Code with the oxc extension (v1.48.0+)
3. Open `src/index.js` — diagnostics appear (eqeqeq, no-console, no-debugger)
4. Open `packages/app-a/src/index.js` — **no diagnostics** shown
5. Run `npx oxlint` from root — CLI correctly reports issues in all files

## Expected

LSP lints files in `packages/app-*/src/` using the root `.oxlintrc.json`.

## Actual

LSP ignores all files inside nested git repos.

## Workaround

Use a `.code-workspace` file with each nested repo as an explicit workspace folder.
