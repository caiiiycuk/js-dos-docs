# Run without bundle

As explained in other topics, the bundle is a preferred way to run DOS program. Bundle can contain much more configuration
that DOSBox excepts, for example, mobile controls, resources, etc.

> If you are looking at the recommended and correct way to run DOS program just create [bundle](jsdos-bundle.md) for it.
> 
{style="warning"}

## Provide dosbox.conf directly

js-dos can work without a bundle, but at least it needs a DOSBox config file to start.
You can create js-dos player by providing config using javascript:

```Javascript
Dos(document.getElementById("app"), {
  dosboxConf: `
    [autoexec]
    mount c .
    c:
  `,
  onEvent: (event, ci) => {
    if (event === "ci-ready") {
        // ci: [[[CommandIterface|command-interface.md]]] is ready
    }
  },
});
```

Having config allows you to start DOSBox / DOSBox-X, but it is not very useful without a program to run.
To init FS before emulation starts, you need to use `initFs` property.
It's a sequence of files, in the following format:

```Javascript
  initFs: [
    { path: string, contents: Uint8Array },
    ...
  ],
```

Where `path` is a full path to file, `contents` is a body of file.

Using the following snippet you will create two files: 
* *1.txt* with contents *"123"*
* *2.txt* in directory *D* with contents *"345"*

```Javascript
Dos(document.getElementById("app"), {
  dosboxConf: `
    [autoexec]
    mount c .
    c:
  `,
  initFs: [
    { path: "1.txt", contents: new TextEncoder().encode("123") },
    { path: "D/2.txt", contents: new TextEncoder().encode("345") },
  ],
  onEvent: (event, ci) => {
    if (event === "ci-ready") {
        // ci: [[[CommandIterface|command-interface.md]]] is ready
    }
  },
});
```

Now when you run it, you can check your files with `TYPE` command:

![](initFs.jpg)

