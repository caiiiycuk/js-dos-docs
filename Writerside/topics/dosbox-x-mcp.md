# DOSBox-X MCP debugger

DOSBox-X MCP lets an AI assistant use the built-in DOSBox-X debugger. It can inspect CPU and memory state,
manage breakpoints and resume execution through a separate local
[dosbox-x-mcp-server](https://github.com/caiiiycuk/dosbox-x-mcp-server) process.

This integration requires a DOSBox-X debug build. It is different from [js-dos MCP](js-dos-mcp.md), which manages
Studio, bundles and standalone projects.

## Connect the MCP server

Download `dosbox-x-mcp-server` for your platform from its
[releases](https://github.com/caiiiycuk/dosbox-x-mcp-server/releases). Configure your MCP client to launch the
executable over stdio, using an absolute path.

For OpenCode V2, merge this into `opencode.jsonc`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "servers": {
      "dosbox": {
        "type": "local",
        "command": ["/absolute/path/dosbox-x-mcp-server"]
      }
    }
  }
}
```

On Windows, use the `.exe` path and escape backslashes in JSON. Preserve unrelated settings.
Use `/mcps` to check the connection.

The MCP client starts the server. DOSBox-X then connects to that process over loopback:

* Native DOSBox-X uses TCP. Call `dosbox_mcp_port` to get its selected port.
* Browser DOSBox-X uses WebSocket. Call `dosbox_mcp_websocket_port` to get its selected port.

The server first tries TCP port `58991` and WebSocket port `58992`. If a port is occupied, it selects another free
port. Always query the matching tool instead of assuming a fixed value. Both port tools work before DOSBox-X connects.
Tool names here are the MCP names; the client may prefix them with the server name.

## Start the browser debug Worker

Use [emulators](emulators.md) and the port returned by `dosbox_mcp_websocket_port`. In this example,
`bundle` contains the initial filesystem data and `websocketPort` is that returned port:

```Javascript
const ci = await emulators.dosboxXDebugWorker(bundle, {
    mcpServerPort: websocketPort,
});
```

The debug Worker connects to `ws://127.0.0.1:<port>/`. It does not expose an MCP HTTP endpoint or listen for
incoming connections in the browser. A direct build or a regular `dosboxXWorker` is not a substitute for the
debug Worker.

When self-hosting, deploy the debug Worker and its WASM assets from the same emulators release as `emulators.js`.
Use `emulators.pathPrefix` to point to that deployment.

In the js-dos player, select **DOSBox-X**, enable **Worker** mode, disable **JSPI**, and set **Debug Port** in
the pre-run settings to the returned WebSocket port. The player selects `dosboxXDebugWorker` automatically.
Debug Port is a player UI setting, not a public `DosOptions` property.

## Native DOSBox-X

Use a native build from the [js-dos DOSBox-X fork](https://github.com/js-dos/dosbox-x) that includes MCP debugger
control. Set `mcp_server` in the `[dosbox]` section to the value returned by `dosbox_mcp_port`. For example,
if that tool returned `58991`:

```ini
[dosbox]
mcp_server=58991
```

Start the MCP server before DOSBox-X. The default `mcp_server=0` disables the control connection.
The browser option `mcpServerPort` configures the corresponding setting for the debug Worker, but needs
the WebSocket port rather than the native TCP port.

## Debugging workflow

First, call `dosbox_ping`. A responsive connected emulator returns `PONG`. An MCP server connection alone does
not mean that DOSBox-X has connected.

Second, call `debug_break` to enter the debugger. Call `debug_help` for the command list supported by the running
build, or `debug_capabilities` for a static catalog that does not require an emulator connection.

Third, inspect state with `debug_snapshot` or run a debugger command with `debug_exec`. For example:

```json
{"command": "CPU"}
```

`debug_snapshot` runs `CPU`, `PIC`, `PAGING`, `EMU MEM` and `EMU MACHINE` in sequence.
`debug_exec` also accepts commands such as `DOS MCBS`, `GDT`, `LDT` and `IDT`.

Use `debug_breakpoint` to manage breakpoints:

| Action | Debugger command |
|--------|------------------|
| `set` | `BP` |
| `int` | `BPINT` |
| `list` | `BPLIST` |
| `delete` | `BPDEL` |
| `mem` | `BPM`, requires `C_HEAVY_DEBUG` |
| `pmem` | `BPPM`, requires `C_HEAVY_DEBUG` |
| `lmem` | `BPLM`, requires `C_HEAVY_DEBUG` |

Pass the command's arguments as `args`. Check `debug_help` for the syntax available in your build.

Finally, call `debug_run` with `mode: "run"`, `"runwatch"` or `"vrt"` to continue execution.
Resume commands acknowledge acceptance before leaving the debugger loop. Their result is not a snapshot
of the state after execution resumes.

## Connection and build limits

The server accepts one active DOSBox-X connection across TCP and WebSocket. Avoid running several debug instances
against the same server. Calls are serialized before reaching the emulator.

DOSBox-X retries the loopback connection if the server is unavailable or the connection is lost.
For a browser build, the MCP process must be reachable on the browser computer's loopback address. Browser local-network
and mixed-content restrictions may also block a connection; inspect the browser console when troubleshooting.

An unavailable emulator or a timeout returns an explicit error. Unknown commands return `ERR`; commands that require
build options may be absent. Query `debug_help` rather than assuming the static catalog matches the running build.
The server writes diagnostics to stderr and `$HOME/.dosbox-x-mcp-server/server.log`.

Use a regular [DOSBox-X backend](DOSBox-X.md) for normal gameplay. Keep the debugger server local and connect only
trusted clients, since debugger commands can inspect and change the emulated program.
