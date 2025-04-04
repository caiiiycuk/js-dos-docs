# Player API

js-dos player provides a single entry point: the `Dos` function, it takes two arguments:
1. element where to create a player window
2. options object to configure player

```Typescript
Dos(element: HTMLDivElement, options: DosOptions) => DosProps;
```

It returns `DosProps` to control the created player 

## Options

| option                  | description                                                                                                                                                                                                                          | type                                                                                                                                                                                                                                                  | default     |
|-------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------|
| **url**                 | url to [js-dos bundle](jsdos-bundle.md)                                                                                                                                                                                              | URL                                                                                                                                                                                                                                                   |             |
| **dosboxConf**          | if you omit url this will be used to configure DOSBox [example](run-without-bundle.md)                                                                                                                                               | str                                                                                                                                                                                                                                                   |             |
| **jsdosConf**           | additionally to dosboxConf you can provide jsdos configuration (jsdos.json)                                                                                                                                                          | Object                                                                                                                                                                                                                                                | {}          |
| **initFs**              | additional files to init FS, [read more](run-without-bundle.md)                                                                                                                                                                      | `Array< { path: string, contents: Uint8Array } >`                                                                                                                                                                                                     |             |
| **background**          | background image of player window                                                                                                                                                                                                    | URL                                                                                                                                                                                                                                                   |             |
| **pathPrefix**          | a way to set different path for [emulators](emulators.md) deployment                                                                                                                                                                 | str                                                                                                                                                                                                                                                   |             |
| **theme**               | the color theme of player                                                                                                                                                                                                            | light, dark, cupcake, bumblebee, emerald, corporate, synthwave, retro, cyberpunk, valentine, halloween, garden, forest, aqua, lofi, pastel, fantasy, wireframe, black, luxury, dracula, cmyk, autumn, business, acid, lemonade, night, coffee, winter | light       |
| **lang**                | language                                                                                                                                                                                                                             | en, ru                                                                                                                                                                                                                                                | auto        |
| **backend**             | default backend                                                                                                                                                                                                                      | dosbox, dosboxX                                                                                                                                                                                                                                       | dosbox      |
| **backendLocked**       | possibility to change backend from UI                                                                                                                                                                                                | bool                                                                                                                                                                                                                                                  | false       |
| **backendHardware**     | reserved                                                                                                                                                                                                                             | -                                                                                                                                                                                                                                                     |             |
| **workerThread**        | use Worker                                                                                                                                                                                                                           | bool                                                                                                                                                                                                                                                  | true        |
| **mouseCapture**        | lock the mouse in player window                                                                                                                                                                                                      | bool                                                                                                                                                                                                                                                  | false       |
| **ipx**                 | ipx backend configuration ([networking](networking.md))                                                                                                                                                                              | Object[]                                                                                                                                                                                                                                              |             |
| **ipxBackend**          | ipx backend name (should be one from ipx array)                                                                                                                                                                                      | str                                                                                                                                                                                                                                                   | netherlands |
| **room**                | ipx room                                                                                                                                                                                                                             | str                                                                                                                                                                                                                                                   |             |
| **fullScreen**          | auto enter fullscreen mode                                                                                                                                                                                                           | bool                                                                                                                                                                                                                                                  | false       |
| **onEvent**             | listener of js-dos events                                                                                                                                                                                                            | function                                                                                                                                                                                                                                              |             |
| **autoStart**           | automatically starts emulation                                                                                                                                                                                                       | boolean                                                                                                                                                                                                                                               | false       |
| **autoSave**            | do auto save of progress on certain actions like exiting fullscreen                                                                                                                                                                  | boolean                                                                                                                                                                                                                                               | false       |
| **kiosk**               | switch kiosk mode, in kiosk mode player ui is hidden                                                                                                                                                                                 | boolean                                                                                                                                                                                                                                               | false       |
| **imageRendering**      | image rendering mode                                                                                                                                                                                                                 | pixelated, smooth                                                                                                                                                                                                                                     | pixelated   |
| **renderBackend**       | the way to render dos screen                                                                                                                                                                                                         | webgl, canvas                                                                                                                                                                                                                                         | webgl       |
| **renderAspect**        | change the aspect of output window                                                                                                                                                                                                   | AsIs, 1/1, 5/4, 4/3, 16/10, 16/9, Fit                                                                                                                                                                                                                 | AsIs        |
| **noNetworking**        | hide networking button                                                                                                                                                                                                               | bool                                                                                                                                                                                                                                                  | true        |
| **noCloud**             | disable cloud feature                                                                                                                                                                                                                | bool                                                                                                                                                                                                                                                  | false       |
| **key**                 | set the key to access cloud services                                                                                                                                                                                                 | string                                                                                                                                                                                                                                                |             |
| **scaleControls**       | set scale of controls                                                                                                                                                                                                                | number                                                                                                                                                                                                                                                | 0.2         |
| **mouseSensitivity**    | set mouse sensitivity                                                                                                                                                                                                                | number                                                                                                                                                                                                                                                | 1.0         |
| **noCursor**            | show/hide system cursor                                                                                                                                                                                                              | bool                                                                                                                                                                                                                                                  | false       |
| **volume**              | set sound volume                                                                                                                                                                                                                     | number [0..1]                                                                                                                                                                                                                                         | 1.0         |
| **softKeyboardLayout**  | initial virtual keyboard layout, [read more](Virtual-Keyboard.md)                                                                                                                                                                    | str[]                                                                                                                                                                                                                                                 | qwerty      |
| **softKeyboardSymbols** | initial virtual keyboard symbols                                                                                                                                                                                                     | map[]                                                                                                                                                                                                                                                 | en, ru      |
| **softFullscreen**      | If set to `true`, fullscreen will not use the `requestFullscreen()` method. Instead, it will add the `jsdos-fullscreen-workaround` class to the root element. You need to ensure that this element occupies the entire page content. | bool                                                                                                                                                                                                                                                  | false       |


> All options are optional
> 
{style="note"}

## DosProps

DosProps are properties that you can use to control player after creation, use it like this:

```Javascript
cost props = Dos(elem, options);
props.setFullScreen(true); // switch to fullscreen mode
```

| property                   | description                                                   | arguments                             |
|----------------------------|---------------------------------------------------------------|---------------------------------------|
| **getVersion**             | version of js-dos + emulators                                 |                                       |
| **getToken**               | get user token or null if not logged                          |                                       |
| **setTheme**               | switch player theme                                           | theme name                            |
| **setLang**                | change language                                               | en, ru                                |
| **setBackend**             | change backend                                                | dosbox, dosboxX                       |
| **setBackendLocked**       | change is backend locked or not                               | bool                                  |
| **setWorkerThread**        | select execution mode                                         | bool                                  |
| **setMouseCapture**        | set is mouse captured or not                                  | bool                                  |
| **setIpx**                 | change ipx backends ([networking](networking.md))             | Object[]                              |
| **setIpxBackend**          | change ipx backend                                            | str                                   |
| **setRoom**                | change ipx room                                               | str                                   |
| **setFrame**               | open named sidebar panel                                      | network                               |
| **setBackground**          | change background image                                       | URL                                   |
| **setFullScreen**          | change fullscreen mode                                        | bool                                  |
| **setAutoStart**           | change auto start                                             | bool                                  |
| **setAutoSave**            | change auto save mode                                         | bool                                  |
| **setKiosk**               | change kiosk mode                                             | bool                                  |
| **setImageRendering**      | change image rendering                                        | pixelated, smooth                     |
| **setRenderBackend**       | change render backend (available only before emulation start) | webgl                                 |
| **setRenderAspect**        | change render aspect                                          | AsIs, 1/1, 5/4, 4/3, 16/10, 16/9, Fit |
| **setNoNetworking**        | show/hide networking button                                   | bool                                  |
| **setNoCloud**             | disable/enable cloud feature                                  | bool                                  |
| **setKey**                 | set key to access cloud services                              | string / null                         |
| **setScaleControls**       | set scale of controls                                         | number                                |
| **setMouseSensitivity**    | set mouse sensitivity                                         | number                                |
| **setPaused**              | pause/resume emulation                                        | bool                                  |
| **setNoCursor**            | show/hide system cursor                                       | bool                                  |
| **setVolume**              | set sound volume                                              | number [0..1]                         |
| **setSoftKeyboardLayout**  | set virtual keyboard layout                                   | string[]                              |
| **setSoftKeyboardSymbols** | set virtual keyboard symbols                                  | map[]                                 |
| **setSoftFullscreen**      | set soft fullscreen                                           | bool                                  |
| **save**                   | trigger to save changes in FS, return `Promise<boolean>`      | -                                     |
| **stop**                   | kill the emulation (use to stop player)                       | -                                     |

## Events

You can listen to js-dos events by providing a listener in the Dos function:

```Javascript
Dos(elem, {
    onEvent: (event , ci?: [[[CommandInterface|command-interface.md]]]) => {
       console.log("js-dos event", event); 
    }
});
```
                    
| order | event                  | description                                                                       | args                                     |
|:------|------------------------|-----------------------------------------------------------------------------------|:-----------------------------------------|
| 0     | **emu-ready**          | when emulators are fully loaded and ready to run the program                      |                                          |
| 1     | **bnd-play**           | when play button is clicked                                                       |                                          |
| 2     | **ci-ready**           | when backend is started and [CommandInterface](command-interface.md) is available | [CommandInterface](command-interface.md) |
|       | **fullscreen-changed** | when fullscreen state of player changed                                           | boolean                                  |


## Stop/Restart player

* To **stop** player and free all used resources, please call `props.stop()`, after this player is totally disposed.
* To **restart** player dispose current player and start new one.

## Typescript

js-dos provide Typescript types [declaration](https://github.com/caiiiycuk/js-dos/blob/8.xx/src/public/types.ts).
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
