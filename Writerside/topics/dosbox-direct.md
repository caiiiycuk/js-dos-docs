# DOSBox Direct

DOSBox Direct is an emulation backend based on DOSBox. You can create it with the following command:

```Javascript
const ci = await emulators.dosboxDirect(bundle);
```

The Direct version is universal and can work in a Node.js environment. Its main disadvantage is that it runs on the main
browser thread, so it can freeze the app for some time and make it less responsive.

> [DOSBox Worker](dosbox-worker.md) is a more preferred version of the emulator backend, because it does not block the browser.
> 
{style="tip"}

### Accessing file system

In direct mode you can easily access the Emscripten module:

```Javascript
const ci = await emulators.dosboxDirect(bundle);
ci.transport.module // <-- emscripten module
```

The Emscripten module provides a low-level API to change the [file system](https://emscripten.org/docs/api_reference/Filesystem-API.html):

```Javascript
const ci = await emulators.dosboxDirect(bundle);
ci.transport.module.FS // <-- emscripten FS api
```

You can also rescan DOS devices:
```Javascript
const ci = await emulators.dosboxDirect(bundle);
ci.transport.module._rescanFilesystem();
```

### Accessing memory

In direct mode you can dump the whole memory of DOS:

```Javascript
const ci = await emulators.dosboxDirect(bundle);
ci.transport.module._dumpMemory(copyDosMemory);
ci.transport.module.memoryContents // <-- now you can access contents using this var
```

If you need to copy the entire memory, pass `true` as argument.
The `memoryContents` contains the following:


```Javascript
{
        "memBase": ...,
        "ip": ...,
        "flags": ...,
        "registers": {
            "ax": ...,
            "cx": ...,
            "dx": ...,
            "sp": ...,
            "bp": ...,
            "si": ...,
            "di": ...
        },
        "segments_values": {
            "es": ...,
            "cs": ...,
            "ss": ...,
            "ds": ...,
            "fs": ...,
            "gs": ...
        },
        "segments_physical": {
            "es": ...,
            "cs": ...,
            "ss": ...,
            "ds": ...,
            "fs": ...,
            "gs": ...
        },
        "numPages": ...,
        "memoryCopy": ...
}
```

### Pausing execution

In direct mode, you can pause the DOSBox execution loop without pausing the
Emscripten loop. This lets you pause and inspect the current memory, for
instance.

```Javascript
const ci = await emulators.dosboxDirect(bundle);
ci.transport.module._pauseExecution(true);
```

The `_pauseExecution` function takes as its argument whether it should be
paused or should resume.  To resume once you have completed:

```Javascript
ci.transport.module._pauseExecution(false);
```
