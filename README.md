# oxc-nested-git-repro

Minimal reproduction for [oxc-project/oxc-vscode#112](https://github.com/oxc-project/oxc-vscode/issues/112).

## Issue

The oxc VS Code extension LSP does not lint files that are listed in `.gitignore`. This is not specific to nested git repos.

## Structure

```
.git/                      ← root git repo
.oxlintrc.json             ← shared lint config
src/index.js               ← linted correctly ✅
packages/app-a/src/index.js  ← NOT linted by LSP (gitignored) ❌
packages/app-b/src/index.js  ← NOT linted by LSP (gitignored) ❌
```

## Steps to reproduce

1. Clone this repo
2. Clone the nested sub-repos:
   ```sh
   git clone git@github.com:Enubia/app-a.git packages/app-a
   git clone git@github.com:Enubia/app-b.git packages/app-b
   ```
3. Open the root folder in VS Code with the oxc extension (v1.48.0+)
4. Comment out `packages/app-*` in `.gitignore`
5. Open `packages/app-a/src/index.js` — diagnostics appear
6. Uncomment `packages/app-*` in `.gitignore`
7. Restart the oxlint server — diagnostics in `packages/app-a/src/index.js` **stop working**
8. Open `src/index.js` — diagnostics still appear (eqeqeq, no-console, no-debugger)
9. Run `npx oxlint` from root — CLI correctly reports issues in all files

## Expected

LSP lints files in `packages/app-*/src/` using the root `.oxlintrc.json`.

## Actual

LSP ignores all files matched by `.gitignore`.

## Workaround

Use a `.code-workspace` file with each nested repo as an explicit workspace folder.
