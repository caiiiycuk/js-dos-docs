# Node

In this tutorial we will run Digger game in Node.js and save game screenshot to the image.

Let's start with creating empty project:

```Bash
npm init
```

To run DOS in browser we need to install [emulators](https://www.npmjs.com/package/emulators) package.
For creating screenshot we will use `jimp` library. So let's install them.

```Bash
npm install --save emulators jimp
``` 

Next we need to download Digger [js-dos bundle](jsdos-bundle.md):
```Bash
curl https://v8.js-dos.com/bundles/digger.jsdos -o digger.jsdos
```

Let's create source file `digger.js`. We can run it with this command `node digger.js`

**Use require to import all libraries**
```Javascript
const fs = require("fs");
const jimp = require("jimp");

require("emulators");

const emulators = global.emulators;
emulators.pathPrefix = "./";
```

<br/>

> emulators package does not use export system. It injects itself into global object.
> `pathPrefix` is used to locate wasm files it relative to `require` path.
>

**Now we need to read contents of `jsdos bundle` and start emulation**
```Javascript
const bundle = fs.readFileSync("digger.jsdos");

emulators
    .dosDirect(bundle)
    .then((ci) => {
      // ...
    });
```

When dos emulation starts, we will receive [Command Interface](command-interface.md), we can use it
to subscribe on frame updates and to send key/mouse events.

```Javascript
    ci.events().onFrame((rgb, rgba) => {
        // use rgb or rgba image data
    });
```

> **onFrame** method have two arguments `rgb` and `rgba` image data. One of them is always **null** while other is 
>  **UInt8ClampedArray**. It depends on used emulator which data it uses rgb or rgba. 
> 
{style="warning"}

In the v7 we have frame in RGBA format with transparent alpha, let's fix this and save screenshot:
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
    .dosDirect(bundle)
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
