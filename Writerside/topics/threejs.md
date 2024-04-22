# How to use without js-dos

In this example you will learn how to use parts of `emulators` with `three.js` renderer.
We will create a rotating cube with a digger game.

![threejs.jpg](threejs.jpg)

First you need to add `emulators.js` and `emulators-ui.js`:
```html
<script src="emulators/emulators.js"></script>
<script src="emulators-ui/emulators-ui.js"></script>
<script>
  emulators.pathPrefix = "emulators/";
```

Then implement renderer:
```html
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
    <title>Digger Example</title>
    <script src="/v7/build/releases/latest/emulators/emulators.js"></script>
    <script src="/v7/build/releases/latest/emulators-ui/emulators-ui.js"></script>
    <style>
        html, body, #root {
            width: 100%;
            height: 100%;
            padding: 0;
            margin: 0;
            overflow: hidden;
        }
    </style>
</head>

<body>
<div id="root">
</div>
<script type="module">
    import * as THREE from 'https://unpkg.com/three@latest/build/three.module.js';
    emulators.pathPrefix = "/v7/build/releases/latest/emulators/";

    const img = document.createElement("canvas");
    img.width = 320;
    img.height = 200;

    const ctx = img.getContext('2d');
    const texture = new THREE.CanvasTexture(img);

    const scene = new THREE.Scene();
    scene.background = new THREE.Color();

    const camera = new THREE.PerspectiveCamera( 75, window.innerWidth/window.innerHeight, 0.1, 1000 );
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize( window.innerWidth, window.innerHeight );

    document.getElementById("root").appendChild(renderer.domElement);

    const geometry = new THREE.BoxGeometry();
    const material = new THREE.MeshBasicMaterial({
        map: texture,
    });
    const cube = new THREE.Mesh( geometry, material );
    scene.add( cube );

    camera.position.z = 2;

    const animate = function () {
            requestAnimationFrame( animate );

            cube.rotation.x += 0.01;
            cube.rotation.y += 0.01;

            renderer.render( scene, camera );
    };

    animate();

    async function runDigger() {
        stop();

        const bundle = await emulatorsUi.network.resolveBundle("https://cdn.dos.zone/original/2X/9/9ed7eb9c2c441f56656692ed4dc7ab28f58503ce.jsdos");
        const ciPromise = emulators.dosWorker(bundle);
        const rgba = new Uint8ClampedArray(320 * 200 * 4);
        ciPromise.then((ci) => {
            emulatorsUi.sound.audioNode(ci);

            ci.events().onFrame((rgb) => {
                for (let next = 0; next < 320 * 200; ++next) {
                    rgba[next * 4 + 0] = rgb[next * 3 + 0];
                    rgba[next * 4 + 1] = rgb[next * 3 + 1];
                    rgba[next * 4 + 2] = rgb[next * 3 + 2];
                    rgba[next * 4 + 3] = 255;
                }

                ctx?.putImageData(new ImageData(rgba, 320, 200), 0, 0);
                texture.needsUpdate = true;
            });


            window.addEventListener("keydown", (e) => {
                const keyCode = emulatorsUi.controls.domToKeyCode(e.keyCode);
                ci.sendKeyEvent(keyCode, true);
            });

            window.addEventListener("keyup", (e) => {
                const keyCode = emulatorsUi.controls.domToKeyCode(e.keyCode);
                ci.sendKeyEvent(keyCode, false);
            });
        });
    }

    runDigger();
</script>
</body>
</html>
```

