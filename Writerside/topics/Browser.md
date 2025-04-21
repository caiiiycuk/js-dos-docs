# Browser

In this example, you will learn how to use `emulators` with `three.js` renderer.
We will create a rotating cube with a digger game.

![threejs.jpg](threejs.jpg)

First, you need to include `emulators.js`:

<tabs>
<tab title="v8">

```Typescript
    <script src="https://v8.js-dos.com/latest/emulators/emulators.js"></script>
```

</tab>
<tab title="v7">

```Typescript
    <script src="https://v8.js-dos.com/v7/emulators/emulators.js"></script>
    <script src="https://v8.js-dos.com/v7/emulators-ui/emulators-ui.js"></script>
```

</tab>
</tabs>

Then set `emulators.pathPrefix` pointing to the correct location:
<tabs>
<tab title="v8">

```Typescript
<script type="module">
    emulators.pathPrefix = "https://v8.js-dos.com/latest/emulators/";
```

</tab>
<tab title="v7">

```Typescript
<script type="module">
    emulators.pathPrefix = "https://v8.js-dos.com/v7/emulators/";
```

</tab>
</tabs>


Then you need to download js-dos bundle (for example, Digger game), and instantiate emulators:
```Typescript
const bundle = await fetch("https://v8.js-dos.com/bundles/digger.jsdos");
const ci = await emulators.dosWorker(new Uint8Array(await bundle.arrayBuffer()));
```

Now the DOS program is started in worker, and we need to render it on the screen.
To do this, we need to subscribe to `frame` event and update texture:

```Typescript
const rgba = new Uint8ClampedArray(320 * 200 * 4);

ci.events().onFrame((rgb) => {
    for (let next = 0; next < 320 * 200; ++next) {
        rgba[next * 4 + 0] = rgb[next * 3 + 0];
        rgba[next * 4 + 1] = rgb[next * 3 + 1];
        rgba[next * 4 + 2] = rgb[next * 3 + 2];
        rgba[next * 4 + 3] = 255;
    }

    ctx?.putImageData(new ImageData(rgba, 320, 200), 0, 0);
    // ...
}
```

Where `rgb` is an actual DOS screen frame.


```Typescript
```
{src="examples/threejs-v8.html" collapsible="true" collapsed-title="Source code"}
