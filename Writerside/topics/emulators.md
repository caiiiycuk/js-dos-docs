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

Example:

```Typescript
const ci = await emulators.dosboxWorker(bundle, {
    onExtractProgress: (bundleIndex, file, extracted, total) => {
        console.log(bundleIndex, file, extracted, total);
    },
    sockdrivePreload: "default",
});
```

`canvas` is used for `OffscreenCanvas` rendering. `audioWorklet` enables the audio worklet path when the browser supports
it. `net` lets you pass a low-level networking instance shared with another backend.

## Bundle utilities

`emulators` can also create and edit bundles:

```Typescript
const bundle = await emulators.bundle();
const config = await emulators.bundleConfig(bundleBytes);
const updated = await emulators.bundleUpdateConfig(bundleBytes, config);
```

## Creating bundles

`emulators.bundle()` creates a `DosBundle` builder. The builder contains default DOSBox config and default `jsdos.json`
metadata. You can add files from zip archives and generate a `.jsdos` bundle as `Uint8Array`.

```Typescript
const bundle = await emulators.bundle();

const bytes = await bundle
    .extract("game.zip", "/")
    .autoexec("GAME.EXE")
    .toUint8Array();
```

`extract()` downloads and extracts a single zip archive. The second argument is the target path inside the DOS file
system.

For multiple archives, use `extractAll()`:

```Typescript
const bytes = await (await emulators.bundle())
    .extractAll([
        { url: "base.zip", path: "/", type: "zip" },
        { url: "patch.zip", path: "/PATCH", type: "zip" },
    ])
    .autoexec("PATCH\\SETUP.EXE")
    .toUint8Array();
```

You can edit config fields before generating the bundle:

```Typescript
const bundle = await emulators.bundle();

bundle.dosboxConf = bundle.dosboxConf.replace("memsize=16", "memsize=32");
bundle.jsdosConf = {
    ...bundle.jsdosConf,
    version: "8",
};

const bytes = await bundle.toUint8Array();
```

By default, config files from extracted archives can override the builder config. Pass `true` to `toUint8Array()` when
the builder config should be written after extraction:

```Typescript
const bytes = await bundle.toUint8Array(true);
```

## Reading and updating bundle config

Use `bundleConfig()` to read `.jsdos/dosbox.conf` and `.jsdos/jsdos.json` from an existing bundle:

```Typescript
const config = await emulators.bundleConfig(bundleBytes);

if (config !== null) {
    console.log(config.dosboxConf);
    console.log(config.jsdosConf);
}
```

Use `bundleUpdateConfig()` to write updated config back into the bundle:

```Typescript
const config = await emulators.bundleConfig(bundleBytes);

if (config !== null) {
    config.dosboxConf += "\n[cpu]\ncycles=max\n";
    const updatedBundle = await emulators.bundleUpdateConfig(bundleBytes, config);
}
```
