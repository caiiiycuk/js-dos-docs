# Working with File System

When js-dos player successfully starts, the DOSBox [command interface](command-interface.md) is available. 
You can get it from the event:

```Javascript
 Dos(/*element*/, {
    url: /* bundle url */,
    onEvent: ("ci-ready", ci: CommandInterface) {
        // now ci s ready
    },
);
```

With ci you can interact with a DOS file system.

## Read file from FS

```Javascript 
fsReadFile(file: string): Promise<Uint8Array>;
```

This function will read the file from the given path and return the promise to Uint8Array.

> You must wait while the promise is resolved, js-dos able to read/write/delete only one file once.
> 
{style="warning"}

> No need to provide a drive letter, files are relative from dosbox start folder.

## Write file to FS

```Javascript
fsWriteFile(file: string, contents: ReadableStream<Uint8Array> | Uint8Array): Promise<void>;
```

This method will write contents into the provided file. 

> * You must wait while the promise is resolved, js-dos able to read/write/delete only one file once.
> * If the file exists, it will be overwritten
> 
{style="warning"}

> Usually you need to remount C: drive to see the changes

## Delete file from FS

```Javascript
fsDeleteFile(file: string): Promise<void>;
```

Delete file from FS.
> You must wait while the promise is resolved, js-dos able to read/write/delete only one file once.
>
{style="warning"}

## Get the whole file tree

You can read the full file system structure by calling:

```Javascript
fsTree(): Promise<FsNode>;
```

The FsNode contains following:
```Javascript
export interface FsNode {
    name: string;
    size: number | null;
    nodes: FsNode[] | null;
}
```

* **name** - is a file name
* **size** - size of file in bytes, or `null` in case of directory
* **nodes** - children of directory, or `null` in case of file

## Advanced: using Emscripten FS API

Emscripten API is even more powerful, but also low level. You can access it only in [direct mode](dosbox-direct.md#accessing-file-system).