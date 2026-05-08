# Node

In this tutorial we will run the Digger game in Node.js and save a game screenshot to an image.

Let's start by creating an empty project:

```Bash
npm init
```

To run DOS in Node.js, we need to install the [emulators](https://www.npmjs.com/package/emulators) package.
To create a screenshot, we will use the `jimp` library, so let's install both packages.

```Bash
npm install --save emulators jimp
``` 

Next we need to download the Digger [js-dos bundle](jsdos-bundle.md):
```Bash
curl https://v8.js-dos.com/bundles/digger.jsdos -o digger.jsdos
```

Let's create the source file `digger.js`. We can run it with `node digger.js`.

**Use require to import all libraries**
```Javascript
const fs = require("fs");
const jimp = require("jimp");

require("emulators");

const emulators = global.emulators;
emulators.pathPrefix = "./";
```

<br/>

> The `emulators` package does not use the export system. It injects itself into the global object.
> `pathPrefix` is used to locate wasm files relative to the `require` path.
>

**Now we need to read the contents of a `js-dos bundle` and start emulation**
```Javascript
const bundle = fs.readFileSync("digger.jsdos");

emulators
    .dosboxDirect(bundle)
    .then((ci) => {
      // ...
    });
```

When DOS emulation starts, we will receive a [Command Interface](command-interface.md). We can use it
to subscribe to frame updates and to send key/mouse events.

```Javascript
    ci.events().onFrame((rgb, rgba) => {
        // use rgb or rgba image data
    });
```

> The **onFrame** method has two arguments: `rgb` and `rgba` image data. One of them is always **null**, while the other is
> **UInt8ClampedArray**. The active format depends on the emulator.
> 
{style="warning"}

In v7, frames are in RGBA format with transparent alpha. Let's fix this and save a screenshot:
```Javascript
    const width = ci.width();
    const height = ci.height();
    
    for (let next = 3; next < width * height * 4; next = next + 4) {
        rgba[next] = 255;
    }

    new jimp({ data: rgba, width, height }, (err, image) => {
        image.write("./screenshot.png", () => {
            ci.exit();
        });
    });
```

<br/>

If you execute `node digger.js` it will save the screenshot to `./screenshot.png`.

Full code of `digger.js`:
```Javascript
const fs = require("fs");
const jimp = require("jimp");

require("emulators");

const emulators = global.emulators;
emulators.pathPrefix = "./";

const bundle = fs.readFileSync("digger.jsdos");

emulators
    .dosboxDirect(bundle)
    .then((ci) => {
        let rgba = new Uint8Array(0);
        ci.events().onFrame((_, _rgba) => {
            rgba = _rgba;
        });

        // capture the screen after 3 sec
        console.log("Will capture screen after 3 sec...");
        setTimeout(() => {
            const width = ci.width();
            const height = ci.height();

            for (let next = 3; next < width * height * 4; next = next + 4) {
                rgba[next] = 255;
            }           

            new jimp({ data: rgba, width, height }, (err, image) => {
                image.write("./screenshot.png", () => {
                    ci.exit();
                });
            });
        }, 3000);
    })
    .catch(console.error);
```
