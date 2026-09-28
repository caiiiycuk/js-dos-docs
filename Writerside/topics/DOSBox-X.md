# DOSBox-X

The DOSBox-X emulation backend is capable of running operating systems up to Windows 98/ME. You can use it to run
Windows games or arbitrary Windows programs. It may be a bit slower than traditional DOSBox emulation.

DOSBox-X emulation supports execution in worker, networking, and all other features that js-dos provides.

Typically, Windows distributions are known for their larger file sizes. To address this, we recommend using virtual makevm drives. 
Additionally, we offer prebuilt images of fully configured Windows 95/98, streamlining the installation process for your chosen game.

Worker instance:
```Javascript
const ci = await emulators.dosboxXWorker(bundle);
```

Direct instance:
```Javascript
const ci = await emulators.dosboxXDirect(bundle);
```

JSPI builds are also available when the browser supports JSPI:

```Javascript
const ci = await emulators.dosboxXJspiWorker(bundle);
// or
const ci = await emulators.dosboxXJspiDirect(bundle);
```

## Browser debugger and MCP

DOSBox-X has a debug Worker build for browser debugger integrations. Start it with `dosboxXDebugWorker` and pass the MCP
server port:

```Javascript
const ci = await emulators.dosboxXDebugWorker(bundle, {
    mcpServerPort: 58992,
});
```

In the js-dos player, set the backend to DOSBox-X, keep Worker mode enabled, leave JSPI disabled, and enter a non-zero
Debug Port in the pre-run settings. The player then uses the debug Worker build automatically.

> The debug build is only for debugging. Use `dosboxXWorker` for normal gameplay.
>
{style="note"}
