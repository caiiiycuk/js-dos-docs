# Releases

## Documented versions

This documentation covers **js-dos 8.5.4**, which uses **emulators 8.5.3**, and **js-dos MCP 0.1.0**.
Player and MCP versions are separate. Use a specific release rather than `latest` when you need a reproducible deployment.

### js-dos 8.5.4

* Added [js-dos MCP](js-dos-mcp.md), a local stdio server with embedded Studio and runtime assets for Linux,
  Windows and macOS Intel/Apple silicon.
* Added the public [player editor API](player-editor.md), complete bundle export, diagnostics and standalone
  HTML, Vite, React, Vue and Angular project generation.
* Added opt-in `runInBackground` and improved player disposal for component unmount/remount.
* Updated emulators to 8.5.3 and hardened MCP ZIP imports with consistent local/central header validation.

### js-dos 8.5.1 to 8.5.3

* Fixed Windows 95 sockdrive hangs caused by incorrect disk size units and browser debugger failures on `RUN`/`VRT`.
* Fixed keyboard/mouse regressions after the DOSBox-X update.
* Fixed WebRTC-NET IPX connection lifecycle and cleanup.

### emulators 8.5.3

Fixed native AudioWorklet resource leaks on shutdown and initialization failure. Worker errors now reach
[Command Interface message subscribers](command-interface.md).

For complete histories, see the
[js-dos changelog](https://github.com/caiiiycuk/js-dos/blob/8.xx/CHANGELOG.md) and
[emulators changelog](https://github.com/caiiiycuk/emulators/blob/8.xx/CHANGELOG.md).

## CDN (v8)

This recommended way to use js-dos, on CDN there is always latest version of js-dos.
Include this into your HTML page:

```html
<!-- js-dos style sheet -->
<link rel="stylesheet" href="https://v8.js-dos.com/latest/js-dos.css">

<!-- js-dos -->
<script src="https://v8.js-dos.com/latest/js-dos.js"></script>
```

That is! DOS constructor is ready to use.

## Github

Player distributions and `mcp-v*` executable releases are available on the
[GitHub releases](https://github.com/caiiiycuk/js-dos/releases) page. Choose the player assets for website embedding,
or the platform-specific MCP archive for [local Studio and agent integration](js-dos-mcp.md).

## Npm

* **js-dos** - [npm package](https://www.npmjs.com/package/js-dos)
* **emulators** - [npm package](https://www.npmjs.com/package/emulators)

If you use `npm` or `yarn`, you can obtain the latest version by using `npm install` or `yarn add`.
All files needed to run emulation are placed in the `dist` folder of packages.
The standalone MCP executable is distributed through GitHub releases, not installed by adding the player npm package.
