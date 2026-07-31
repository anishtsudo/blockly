# AGENTS.md

## Cursor Cloud specific instructions

This is the Raspberry Pi Foundation fork of the Blockly monorepo (npm workspaces).
The primary product is the `blockly` library in `packages/blockly`; the rest of
`packages/plugins/*` and `packages/docs` are supporting workspaces.

Dependencies are installed at the repo root via `npm install` (handled by the
startup update script). Node >= 22 is required (see `packages/blockly/package.json`
`engines`). `postinstall` runs `patch-package` automatically.

### Building

- Full monorepo build (core package + docs + all workspaces): `npm run build` from
  the repo root. This is slow (a couple of minutes) and the docs step prints many
  "Broken link" warnings — these are expected and do not fail the build.
- Core library only (faster): `cd packages/blockly && npm run build` (runs `gulp build`).
- The playground and browser tests require the core build output in
  `packages/blockly/build/`, so build before serving/testing.

### Linting / formatting

- Lint everything: `npm run lint` from the repo root (fans out to workspaces), or
  `cd packages/blockly && npm run lint` for just the core package.
- Format check: `npm run format:check` (uses Prettier).

### Testing (needs a display + Chrome)

The core test suite (`cd packages/blockly && npm run test`, i.e. `gulp test`) runs
browser tests via webdriverio against real Chrome, so it needs a virtual X display:

```bash
Xvfb :99 -screen 0 1280x1024x24 &
export DISPLAY=:99
export CHROME_BIN=$(which google-chrome)   # /usr/local/bin/google-chrome
cd packages/blockly && CI=true npm run test
```

Without `DISPLAY`/Xvfb the browser-based test stages will fail to launch Chrome.

### Running the app (playground / demos)

The interactive development UI is the Blockly playground. `npm start` (in
`packages/blockly`) rebuilds and then serves via `http-server`; to avoid the rebuild
when the package is already built, serve directly:

```bash
cd packages/blockly
npx http-server ./ -s -p 8080 -c-1
# then open http://localhost:8080/tests/playground.html
```

Static demos are under `packages/blockly/demos/` (e.g. `/demos/code/index.html`) and
are served by the same `http-server`.
