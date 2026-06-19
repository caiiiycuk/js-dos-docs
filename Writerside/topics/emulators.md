# emulators

`emulators` is an [npm package](https://www.npmjs.com/package/emulators) that provides emulator backends for different
environments. It is the core of js-dos: `emulators` has a standardized API and can run emulation in browsers, workers,
and Node.js. It is intended for custom embedding.

<img alt="emulators-logo.jpg" height="256" src="emulators-logo.jpg" title="Emulators Logo" width="256"/>

## Deployment options

When you use `emulators.js` directly, the package is exposed as a global `emulators` object.

```Typescript
emulators.pathPrefix = "https://v8.js-dos.com/latest/emulators/";
emulators.pathSuffix = "";
```

| property       | description                                                                 |
|----------------|-----------------------------------------------------------------------------|
| **pathPrefix** | Prefix used to load emulator deployment files such as wasm and worker files. |
| **pathSuffix** | Suffix appended to each deployment file path.                                |
| **version**    | Version of the loaded emulators build.                                       |
| **wdosboxJs**  | DOSBox worker script file name.                                              |

## Starting backend

Every backend factory accepts initial file system data and returns a [CommandInterface](command-interface.md).

```Typescript
const ci = await emulators.dosboxWorker(bundle);
```

Available backend factories:

| function             | description                                      |
|----------------------|--------------------------------------------------|
| **dosboxDirect**     | Starts DOSBox in the current thread.             |
| **dosboxWorker**     | Starts DOSBox in a Worker.                       |
| **dosboxNode**       | Starts DOSBox in Node.js.                        |
| **dosboxXDirect**    | Starts DOSBox-X in the current thread.           |
| **dosboxXWorker**    | Starts DOSBox-X in a Worker.                     |
| **dosboxXJspiWorker**| Starts DOSBox-X JSPI build in a Worker.          |
| **dosboxXNode**      | Starts DOSBox-X in Node.js.                      |
| **backend**          | Starts a backend from a custom transport layer.  |

## InitFs

Backend factories accept a bundle, a file entry, a DOS config, a string, or an array of these entries.

```Typescript
type InitFsEntry = Uint8Array | { path: string, contents: Uint8Array } | DosConfig | string;
type InitFs = InitFsEntry | InitFsEntry[];
```

## BackendOptions

```Typescript
interface BackendOptions {
    token?: string;
    onExtractProgress?: (bundleIndex: number, file: string, extracted: number, total: number) => void;
    canvas?: OffscreenCanvas;
    audioWorklet?: boolean;
    sockdrivePreload?: "all" | "default" | "none";
    net?: Net;
}
```

## Bundle utilities

`emulators` can also create and edit bundles:

```Typescript
const bundle = await emulators.bundle();
const config = await emulators.bundleConfig(bundleBytes);
const updated = await emulators.bundleUpdateConfig(bundleBytes, config);
```
