# MCP

js-dos MCP lets an AI assistant create, edit, run and export DOS bundles through the
[Model Context Protocol](https://modelcontextprotocol.io/). It includes a local Studio that runs in your browser
and can generate standalone websites with a js-dos player.

The executable includes Studio, DOSBox, DOSBox-X and the runtime assets. Running it does not require Node.js,
npm, Rust or a browser automation framework. A local browser is still required to run a bundle.

For CPU state, breakpoints and debugger commands, use the separate [DOSBox-X MCP](dosbox-x-mcp.md) integration.

## Install

Download the archive for your system and its `.sha256` file from the
[js-dos releases](https://github.com/caiiiycuk/js-dos/releases). MCP releases use `mcp-v*` tags, separately from
the js-dos player version.

| Archive | System requirements |
|---------|---------------------|
| `jsdos-mcp-linux-x86_64.tar.gz` | Linux x86_64 with glibc 2.35 or newer |
| `jsdos-mcp-windows-x86_64.tar.gz` | Windows 10/11 x86_64 |
| `jsdos-mcp-macos-x86_64.tar.gz` | macOS 15 or newer, Intel |
| `jsdos-mcp-macos-arm64.tar.gz` | macOS 15 or newer, Apple silicon |

For example, on Linux:

```bash
sha256sum -c jsdos-mcp-linux-x86_64.sha256
tar -xzf jsdos-mcp-linux-x86_64.tar.gz
chmod +x jsdos-mcp/jsdos-mcp
```

On macOS, compare the checksum with `shasum -a 256`. On Windows, use `Get-FileHash -Algorithm SHA256`.
Windows uses `jsdos-mcp.exe` and may require the Microsoft Visual C++ runtime. macOS may ask you to approve
the unsigned executable.

Use a current browser with WebAssembly, workers, canvas and WebSocket support. On Linux, automatic browser opening
requires a graphical desktop and `xdg-open`; otherwise open the returned Studio URL manually on the same machine.
The URL points to loopback, so a browser on another computer cannot open it without a separately arranged connection.

## Connect an MCP client

Configure your MCP client to launch the executable over **stdio**, using its absolute path. The client starts
and stops the process. Do not configure the Studio URL as an HTTP MCP endpoint.

For OpenCode V2, merge this entry into `opencode.jsonc`, preserving your other settings:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "servers": {
      "jsdos": {
        "type": "local",
        "command": ["/absolute/path/jsdos-mcp/jsdos-mcp"],
        "timeout": { "execution": 150000 }
      }
    }
  }
}
```

On Windows, use the absolute path to `jsdos-mcp.exe` and escape backslashes in JSON. OpenCode reloads configuration
after a save. Use `/mcps` or `opencode mcp list` to check the connection. See the
[OpenCode MCP guide](https://opencode.ai/docs/mcp-servers) for client configuration.

## Available tools

Tool names below are the MCP names. Your client may add the server name as a prefix.

| Tool | Purpose |
|------|---------|
| `studio` | Return the local Studio URL and connection status; optionally open the browser |
| `editor` | Create or open a bundle, change configuration and files, start, restart, export or recover |
| `diagnose` | Validate a selected bundle or observe the connected emulator |
| `scenario` | Send keyboard and mouse input and wait for a stdout substring |
| `screenshot` | Return the latest emulator frame as a PNG |
| `generate` | Create an HTML, Vite, React, Vue or Angular project |
| `verify_project` | Check a generated project's manifest and optionally run its build |
| `preview_project` | Start, inspect or stop an explicitly requested browser diagnostic preview |

## Create and run a bundle

First, call `studio` with:

```json
{"open_browser": true}
```

If automatic opening fails, use the returned URL. Wait until Studio reports `connected`, then call `editor`:

```json
{"operation": "state"}
```

The state includes the current `revision`, draft and running configuration, emulator readiness, pause, visibility
and audio state. Read state before editing and pass its revision as `expected_revision`.

For example, if the current revision is `0`, create a bundle:

```json
{
  "operation": "create",
  "dosbox_conf": "[autoexec]\n@echo off\necho MCP_READY\n",
  "expected_revision": 0
}
```

Read the resulting revision and use it to start:

```json
{"operation": "start", "expected_revision": 1}
```

The revision numbers in these examples assume no other edits. UI and MCP edits share the same revision checks.
After a conflict, read state again instead of retrying with the old revision.

To import an existing bundle, use `open` with an explicitly selected `source` ZIP or directory. A directory must
contain `.jsdos/dosbox.conf`. If `.jsdos/jsdos.json` is present, it must be valid JSON with a version.
Replacing unsaved edits requires `discard_changes: true`.

Use `config` to change `dosbox_conf` and `jsdos_version`. Changes remain in the draft until the next start or restart.
The MCP `config` operation replaces `jsdos.json` with the supplied version field; it does not preserve additional
metadata such as control layouts. Use Studio or the full [browser editor API](player-editor.md) when those fields
must be retained.

Use `tree`, `read` and `files` only after starting the emulator. File access does not start it implicitly.
The `files` operation accepts entries with a relative `path` and either `contents_base64` or `delete: true`.

## Export and recover

Export explicitly to an existing parent directory, using the current revision:

```json
{
  "operation": "export",
  "destination": "/absolute/path/game.jsdos",
  "expected_revision": 2,
  "replace": false
}
```

Export writes a complete ZIP containing the current files and draft configuration. It is different from
[saving filesystem changes](Save-Load-Game-Progress.md). An existing destination requires `replace: true`;
links and directories are rejected. Failed exports do not replace an existing file with a partial archive.

> External sockdrive and qcow2 disk resources are not embedded in the exported bundle. Check the returned warnings
> before treating a bundle as standalone.
>
{style="warning"}

Closing a tab loses unsaved browser memory. The server retains the original import and the last confirmed complete
snapshot, not every edit. Reopen the URL returned by `studio`, read state and explicitly call `recover` with
`expected_revision` and any required discard permission. Reconnecting does not replay interrupted operations.
If an operation has an unknown outcome, inspect state before retrying.

## Diagnostics and input

Call `diagnose` with `source` for static ZIP/configuration validation. It does not launch the bundle and reports
runtime checking as `not-run`. Without `source`, it observes the connected Studio. Optional `wait_ready_ms` is
limited to 30000 and does not start the emulator.

Reports distinguish invalid input, missing runtime checks, timeouts, errors and observed readiness. A ready
Command Interface does not prove that an arbitrary game works correctly. Reports include bounded stdout and backend
messages. DOSBox `[LOG_*]` messages retain their severity but do not by themselves indicate a browser backend failure.

`scenario` accepts up to 128 `key`, `mouse`, `button`, `wait` and `state` steps, plus a required nonempty `stdout`
substring and `timeout_ms` of at most 30000. Each wait is limited to 10000 ms. For example:

```json
{
  "steps": [
    {"type": "key", "key_code": 13, "pressed": true},
    {"type": "key", "key_code": 13, "pressed": false}
  ],
  "stdout": "MCP_READY",
  "timeout_ms": 5000
}
```

Scenario keys use supported DOM numeric keycodes, such as `13` for Enter, not the low-level
[Command Interface key codes](command-interface.md). Mouse coordinates are normalized from 0 to 1.
Held inputs are released when the scenario ends. Arbitrary Javascript and shell steps are not supported.

`screenshot` returns a real PNG frame or `no-frame` before the first observed frame. Studio enables
`runInBackground`, but browser throttling and audio autoplay restrictions still apply. Audio state is reported
separately from emulator readiness; `backend-managed` does not prove that audio is playing.

## Generate a standalone project

Call `generate` with a selected bundle and a destination directory that does not already exist:

```json
{
  "source": "/absolute/path/game.jsdos",
  "destination": "/absolute/path/game-site",
  "preset": "html"
}
```

Available presets are `html`, `vite`, `react`, `vue` and `angular`. Each project includes the bundle, local runtime
assets, a manifest and license notices. Generation does not run npm and needs no Node.js.

The HTML preset needs only an HTTP server. For example, run this inside the generated directory:

```bash
python3 -m http.server 8080 --bind 127.0.0.1
```

Framework projects require Node.js 22.12 or newer in the 22 series, or 20.19 or newer in the 20 series.
Install dependencies explicitly, then use `npm run dev`, `npm run build` or `npm run preview`.
Vite builds to `dist`; Angular builds to `dist/player/browser`. Do not open projects using `file://`.

Generated projects keep working after MCP shutdown and do not require the Studio control channel or a CDN.
External disk resources are not copied, so bundles that use them still need those resources.

## Verify a project

`verify_project` takes `project` and `timeout_seconds` from 1 to 120. It checks the manifest, required files
and checksums without installing packages. Builds remain `not-run` unless you explicitly set `allow_build_scripts: true`.

> Build permission allows `npm run build` to execute arbitrary local package scripts. It is not a sandbox.
> Give permission only for a project you trust.
>
{style="warning"}

`preview_project` with `operation: "start"` serves a selected HTML or production build `directory`. For a Vite
or Angular dev server, start that server yourself and supply the project directory plus its exact
`http://127.0.0.1:PORT` origin as `upstream`. Use `npm run dev -- --mode diagnostic` for Vite or
`npm run dev -- --configuration diagnostic` for Angular when using their diagnostic hooks.

Open the URL returned by the preview tool. It adds temporary browser diagnostics to HTTP responses without changing
project files. Use `operation: "report"` to inspect browser errors and failed resources, then `operation: "stop"`
to close the preview. A report remains `not-run` until a browser connects. A static or build check is not a browser check.

## Local security and limits

Studio binds only to `127.0.0.1` and protects its control connection with origin checks and a session secret.
The Studio URL contains that secret in its fragment; keep the original URL private, including when sharing logs.
The browser removes the fragment from the address bar, but reopening or reloading Studio requires the original URL.

ZIP transfers are limited to 64 MiB, expanded contents to 256 MiB and entries to 4096. Imports reject unsafe paths,
links, duplicate or case-conflicting names and inconsistent ZIP headers.

Stopping the MCP process closes its local servers, control connections, previews and managed builds. Exported bundles
and generated projects remain on disk. The user's browser tab is not forcibly closed.
