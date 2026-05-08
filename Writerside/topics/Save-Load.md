# Persist FS

`emulators` supports saving and restoring changes made in the file system. It works by dumping
file system changes into a second `bundle` and using it to override the original file system
on next load. This feature is backed by [CommandInterface](command-interface.md) `persist` function.

You can implement your own save/load feature like this:

```Typescript
const bundle = <Uint8Array>;
const ci = await emulators.dosboxWorker(bundle);

// saving
const changesBundle = await ci.persist();

// <new session>

// loading
const ci = await emulators.dosboxWorker([bundle, changesBundle]);
```
