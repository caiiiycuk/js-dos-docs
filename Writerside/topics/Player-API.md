# Player API

The js-dos player provides a single entry point: the `Dos` function. It takes two arguments:
1. the element where the player window should be created
2. an options object to configure the player

```Typescript
Dos(element: HTMLDivElement, options: DosOptions) => DosProps;
```

It returns `DosProps`, which you can use to control the created player.

## Options

| option                  | description                                                                                                                                                                                                                          | type                                                                                                                                                                                                                                                  | default                  |
|-------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------|
| **url**                 | URL to a [js-dos bundle](jsdos-bundle.md)                                                                                                                                                                                            | URL                                                                                                                                                                                                                                                   |                          |
| **dosboxConf**          | If you omit `url`, this will be used to configure DOSBox. See [example](run-without-bundle.md).                                                                                                                                      | string                                                                                                                                                                                                                                                |                          |
| **jsdosConf**           | Additional js-dos configuration (`jsdos.json`) used with `dosboxConf`.                                                                                                                                                               | object                                                                                                                                                                                                                                                | `{}`                     |
| **initFs**              | Additional files to initialize the file system. [Read more](run-without-bundle.md).                                                                                                                                                  | `Uint8Array`, `{ path: string, contents: Uint8Array }`, or an array of these entries                                                                                                                                                                  |                          |
| **background**          | Background image of the player window.                                                                                                                                                                                               | URL                                                                                                                                                                                                                                                   |                          |
| **pathPrefix**          | Path prefix for [emulators](emulators.md) deployment files.                                                                                                                                                                          | string                                                                                                                                                                                                                                                | `https://v8.js-dos.com/latest/emulators/` |
| **pathSuffix**          | Suffix appended to emulator deployment file paths.                                                                                                                                                                                   | string                                                                                                                                                                                                                                                | `""`                     |
| **theme**               | Color theme of the player.                                                                                                                                                                                                           | light, dark, cupcake, bumblebee, emerald, corporate, synthwave, retro, cyberpunk, valentine, halloween, garden, forest, aqua, lofi, pastel, fantasy, wireframe, black, luxury, dracula, cmyk, autumn, business, acid, lemonade, night, coffee, winter | light or dark by system preference |
| **lang**                | Player language.                                                                                                                                                                                                                     | en, ru                                                                                                                                                                                                                                                | auto                     |
| **backend**             | Default backend.                                                                                                                                                                                                                     | dosbox, dosboxX                                                                                                                                                                                                                                       | dosbox                   |
| **backendLocked**       | Prevents changing the backend from the UI.                                                                                                                                                                                           | boolean                                                                                                                                                                                                                                               | false                    |
| **backendHardware**     | Function that returns a hardware backend endpoint for the selected backend.                                                                                                                                                           | `(backend: "dosbox" \| "dosboxX") => Promise<string \| null>`                                                                                                                                                                                         |                          |
| **workerThread**        | Run the backend in a Worker.                                                                                                                                                                                                         | boolean                                                                                                                                                                                                                                               | true                     |
| **offscreenCanvas**     | Use `OffscreenCanvas` for rendering when available.                                                                                                                                                                                  | boolean                                                                                                                                                                                                                                               | false                    |
| **mouseCapture**        | Lock the mouse in the player window.                                                                                                                                                                                                 | boolean                                                                                                                                                                                                                                               | false                    |
| **fullScreen**          | Automatically enter fullscreen mode.                                                                                                                                                                                                 | boolean                                                                                                                                                                                                                                               | false                    |
| **onEvent**             | Listener for js-dos events.                                                                                                                                                                                                          | function                                                                                                                                                                                                                                              |                          |
| **autoStart**           | Automatically start emulation.                                                                                                                                                                                                       | boolean                                                                                                                                                                                                                                               | false                    |
| **fastForwardOnBoot**   | Fast-forward boot for the specified number of seconds when a sockdrive bundle is used.                                                                                                                                               | number                                                                                                                                                                                                                                                | 0                        |
| **countDownStart**      | Set countdown before auto start, in seconds.                                                                                                                                                                                         | number                                                                                                                                                                                                                                                | 0                        |
| **autoSave**            | Automatically save progress on certain actions, such as exiting fullscreen.                                                                                                                                                          | boolean                                                                                                                                                                                                                                               | false                    |
| **kiosk**               | Enable kiosk mode; in kiosk mode the player UI is hidden.                                                                                                                                                                            | boolean                                                                                                                                                                                                                                               | false                    |
| **imageRendering**      | Image rendering mode.                                                                                                                                                                                                                | pixelated, smooth                                                                                                                                                                                                                                     | pixelated                |
| **renderBackend**       | Renderer for the DOS screen.                                                                                                                                                                                                         | webgl, canvas                                                                                                                                                                                                                                         | webgl                    |
| **renderAspect**        | Aspect ratio of the output window.                                                                                                                                                                                                   | AsIs, 1/1, 5/4, 4/3, 16/10, 16/9, Fit                                                                                                                                                                                                                 | AsIs                     |
| **scaleControls**       | Scale of controls.                                                                                                                                                                                                                   | number                                                                                                                                                                                                                                                | 0.2                      |
| **mouseSensitivity**    | Mouse sensitivity.                                                                                                                                                                                                                   | number                                                                                                                                                                                                                                                | 0.5                      |
| **noCursor**            | Hide the system cursor.                                                                                                                                                                                                              | boolean                                                                                                                                                                                                                                               | false                    |
| **volume**              | Sound volume.                                                                                                                                                                                                                        | number [0..1]                                                                                                                                                                                                                                         | 1.0                      |
| **softKeyboardLayout**  | Initial virtual keyboard layout. [Read more](Virtual-Keyboard.md).                                                                                                                                                                  | `string[] \| string[][][]`                                                                                                                                                                                                                            | built-in qwerty layout   |
| **softKeyboardSymbols** | Initial virtual keyboard symbols.                                                                                                                                                                                                    | `Array<Record<string, string>>`                                                                                                                                                                                                                       | built-in symbols         |
| **softFullscreen**      | If set to `true`, fullscreen will not use the `requestFullscreen()` method. Instead, it will add the `jsdos-fullscreen-workaround` class to the root element. You need to ensure that this element occupies the entire page content. | boolean                                                                                                                                                                                                                                               | false                    |
| **thinSidebar**         | Start the player with the thin sidebar.                                                                                                                                                                                              | boolean                                                                                                                                                                                                                                               | false                    |
| **sockdrivePreload**    | Controls sockdrive preloading.                                                                                                                                                                                                       | none, all, default                                                                                                                                                                                                                                    | default                  |
| **startIpxServer**      | Start an IPX server for multiplayer networking.                                                                                                                                                                                      | boolean                                                                                                                                                                                                                                               | false                    |
| **connectIpxAddress**   | Connect to an IPX peer ID or registered alias.                                                                                                                                                                                       | string                                                                                                                                                                                                                                                |                          |
| **fsChanges**           | Options to configure [save/load](Save-Load-Game-Progress.md).                                                                                                                                                                       | object                                                                                                                                                                                                                                                | `{ local: true }`        |
| **net**                 | WebRTC networking configuration.                                                                                                                                                                                                     | `{ peerServer: string, token: string, secret: string, iceServers: () => Promise<IceServer[]> }`                                                                                                                                                       | js-dos defaults          |


> All options are optional
> 
{style="note"}

## DosProps

`DosProps` are properties that you can use to control the player after creation. Use them like this:

```Javascript
const props = Dos(elem, options);
props.setFullScreen(true); // switch to fullscreen mode
```

| property                   | description                                                          | arguments                             |
|----------------------------|----------------------------------------------------------------------|---------------------------------------|
| **getVersion**             | version of js-dos + emulators                                        |                                       |
| **getLocalChanges**        | return current FS changes [read more](Save-Load-Game-Progress.md)    | key                                   |
| **setTheme**               | switch player theme                                                  | theme name                            |
| **setLang**                | change language                                                      | en, ru                                |
| **setBackend**             | change backend                                                       | dosbox, dosboxX                       |
| **setBackendLocked**       | lock or unlock backend selection                                     | bool                                  |
| **setWorkerThread**        | select execution mode                                                | bool                                  |
| **setOffscreenCanvas**     | enable or disable `OffscreenCanvas` rendering                         | bool                                  |
| **setMouseCapture**        | enable or disable mouse capture                                      | bool                                  |
| **setBackground**          | change background image                                              | URL                                   |
| **setFullScreen**          | change fullscreen mode                                               | bool                                  |
| **setAutoStart**           | change auto start                                                    | bool                                  |
| **setCountDownStart**      | set countdown to auto start in seconds                               | number                                |
| **setAutoSave**            | change auto save mode                                                | bool                                  |
| **setKiosk**               | change kiosk mode                                                    | bool                                  |
| **setImageRendering**      | change image rendering                                               | pixelated, smooth                     |
| **setRenderBackend**       | change render backend (available only before emulation start)        | webgl                                 |
| **setRenderAspect**        | change render aspect                                                 | AsIs, 1/1, 5/4, 4/3, 16/10, 16/9, Fit |
| **setNoCloud**             | disable/enable cloud feature                                         | bool                                  |
| **setScaleControls**       | set scale of controls                                                | number                                |
| **setMouseSensitivity**    | set mouse sensitivity                                                | number                                |
| **setPaused**              | pause/resume emulation                                               | bool                                  |
| **setNoCursor**            | show/hide system cursor                                              | bool                                  |
| **setVolume**              | set sound volume                                                     | number [0..1]                         |
| **setSoftKeyboardLayout**  | set virtual keyboard layout                                          | string[]                              |
| **setSoftKeyboardSymbols** | set virtual keyboard symbols                                         | map[]                                 |
| **setSoftFullscreen**      | set soft fullscreen                                                  | bool                                  |
| **setThinSidebar**         | switch between normal and thin sidebar                               | bool                                  |
| **save**                   | trigger saving FS changes, returns `Promise<boolean>`                | -                                     |
| **stop**                   | kill the emulation (use to stop player)                              | -                                     |

## Events

You can listen to js-dos events by providing a listener in the Dos function:

```Javascript
Dos(elem, {
    onEvent: (event, ci?: [[[CommandInterface|command-interface.md]]]) => {
       console.log("js-dos event", event); 
    }
});
```
                    
| order | event                  | description                                                                       | args                                     |
|:------|------------------------|-----------------------------------------------------------------------------------|:-----------------------------------------|
| 0     | **emu-ready**          | when emulators are fully loaded and ready to run the program                      |                                          |
| 1     | **bnd-play**           | when play button is clicked                                                       |                                          |
| 2     | **ci-ready**           | when backend is started and [CommandInterface](command-interface.md) is available | [CommandInterface](command-interface.md) |
|       | **fullscreen-change**  | when fullscreen state of player changed                                           | boolean                                  |


## Stop/Restart player

* To **stop** player and free all used resources, please call `props.stop()`, after this player is totally disposed.
* To **restart** player dispose current player and start new one.

## Typescript

js-dos provides Typescript type [declarations](https://github.com/caiiiycuk/js-dos/blob/8.xx/src/public/types.ts).
Just put it somewhere in your codebase, and declare Dos function:

```Typescript
import {DosFn} from "types";

declare const Dos: DosFn;

// ...

Dos(element, options)
```

```Typescript
```
{src="js-dos/src/public/types.ts" collapsed-title="types.ts" collapsible="true"}
