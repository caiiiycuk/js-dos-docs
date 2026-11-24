# Player editor API

Starting with js-dos 8.5.4, `DosProps.editor` provides programmatic bundle editing in the browser. It is also
the editor API used by [js-dos MCP](js-dos-mcp.md).

Use it to open a bundle, edit configuration and files, start or restart emulation and capture a complete bundle.
For low-level emulator control, use the [Command Interface](command-interface.md).

## Open, edit and export

In this example, `player` is an existing `DosProps` instance whose `emu-ready` event has fired, and `bundleBytes`
is an existing `.jsdos` ZIP as a `Uint8Array`. Keep automatic start disabled for this workflow.
Run the asynchronous example inside a module or an async function:

```Javascript
const opened = await player.editor.open(bundleBytes);
const running = await player.editor.start({ expectedRevision: opened.revision });
const edited = await player.editor.files([
    { path: "MESSAGE.TXT", contents: new TextEncoder().encode("Hello from js-dos") },
], { expectedRevision: running.revision });
const snapshot = await player.editor.snapshot({ expectedRevision: edited.revision });
console.log(snapshot.bundle, snapshot.warnings);
```

`snapshot.bundle` contains the complete ZIP as a `Uint8Array`; your application must download or store it.
Taking a snapshot does not write a file on the user's computer or save progress to OPFS.

## Methods

| Method | Result and behavior |
|--------|---------------------|
| `state()` | Return the current `DosEditorState` synchronously |
| `open(bundle, options?)` | Open a nonempty bundle ZIP and return state; stop the current emulator if necessary |
| `setConfig(config, options?)` | Update draft configuration and return state |
| `tree()` | Return the running emulator's filesystem tree |
| `readFile(path)` | Return a file as a `Uint8Array` |
| `files(operations, options?)` | Write or delete files in the running emulator and return state |
| `start(options?)` | Start the open bundle and wait for the Command Interface |
| `restart(options?)` | Capture current files and draft configuration, then restart from that complete snapshot |
| `snapshot(options?)` | Return `{ bundle, revision, warnings }` without disposing the player |

All methods except `state()` return promises. The declarations are included in [Player API](Player-API.md).

`setConfig` accepts `{ dosboxConf: string, jsdosConf: { version: string, ... } }`. It changes the draft, not the
configuration of an already running emulator. Use `restart()` to apply it to the running program.

`tree()`, `readFile()` and `files()` require a ready emulator; they never start it implicitly.
`start()` rejects an already running emulator. Use `restart()` instead.

File operations have one of these forms:

```Typescript
type DosEditorFileOperation =
    { path: string; contents: Uint8Array | ReadableStream<Uint8Array> } |
    { path: string; delete: true };
```

Paths must be relative to the emulator filesystem, without leading slashes, backslashes, drive prefixes or
`.` and `..` components.

## State and revisions

`state()` returns the current `revision`, `dirty`, bundle name, `ready`, loading `step`, `error`, draft `config`
and `runningConfig`. It also reports `paused`, `hidden`, `runInBackground`, `audio` and `volume`.
The configurations are `null` when unavailable.

UI edits and API operations use the same queue. Pass `expectedRevision` to reject an operation if the editor changed
since you read state:

```Javascript
await player.editor.restart({
    expectedRevision: player.editor.state().revision,
});
```

`expectedRevision` is optional in the browser API, but required for MCP edits as `expected_revision`.
After a conflict, read state again and decide whether your edit is still appropriate. Do not blindly retry.
The revision tracks editor operations, not every filesystem write made by the DOS program.

Opening another bundle over unsaved edits requires `discardChanges: true` in the `open` options.
Do not set it unless the user intends to discard those edits. A snapshot does not clear the dirty flag.

## Complete snapshots and saves

Before launch, a snapshot uses the retained bundle archive. If pending filesystem changes or `initFs` entries
still need merging, start the bundle before exporting it.

After launch, a snapshot captures the filesystem using `persist(false)` and writes the draft configuration into the ZIP.
The editor pauses emulation during capture and restores the previous pause policy, including when capture fails.

Snapshots contain filesystem contents and configuration, not a CPU/RAM save state. External sockdrive or qcow2 disks
are not embedded; inspect `warnings` before treating the result as standalone.

`player.save()` still saves only filesystem changes through [save storage](Save-Load-Game-Progress.md).
Use `editor.snapshot()` when you need a complete `.jsdos` bundle.

## Errors and cleanup

Editor errors expose a `code`, `message` and current `revision`. Codes are `invalid-input`, `conflict`,
`not-ready`, `backend` and `timeout`. A failed `files()` batch may have applied earlier entries; its `completed`
array lists the paths that succeeded. File batches are not transactions.

`start()` waits up to 30 seconds for readiness. A timeout does not prove that initialization stopped. Inspect state
before repeating a start or another operation whose outcome is uncertain.

Call `await player.stop()` to dispose the whole player, including pending or active emulation. Editor operations
reject after disposal. To restart only the bundle while keeping the player, use `editor.restart()`.
