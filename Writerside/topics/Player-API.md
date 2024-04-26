# Player API

js-dos player provide single entry point a `Dos` function, it takes two arguments:
1. element where to create player window
2. options object to configure player

```Typescript
Dos(element: HTMLDivElement, options: DosOptions) => DosProps;
```

It returns `DosProps` to control created object

## Options

| option              | description                                                          | type                                                                                                                                                                                                                                                  | default     |
|---------------------|----------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------|
| **url**             | url to [js-dos bundle](jsdos-bundle.md)                              | URL                                                                                                                                                                                                                                                   |             |
| **dosboxConf**      | if you omit url this will be used to configure DOSBox                | str                                                                                                                                                                                                                                                   |             |
| **background**      | background image of player window                                    | URL                                                                                                                                                                                                                                                   |             |
| **pathPrefix**      | a way to set different path for [emulators](emulators.md) deployment | str                                                                                                                                                                                                                                                   |             |
| **theme**           | the color theme of player                                            | light, dark, cupcake, bumblebee, emerald, corporate, synthwave, retro, cyberpunk, valentine, halloween, garden, forest, aqua, lofi, pastel, fantasy, wireframe, black, luxury, dracula, cmyk, autumn, business, acid, lemonade, night, coffee, winter | dark        |
| **lang**            | language                                                             | en, ru                                                                                                                                                                                                                                                | auto        |
| **backend**         | default backend                                                      | dosbox, dosboxX                                                                                                                                                                                                                                       | dosbox      |
| **backendLocked**   | possibility to change backend from UI                                | bool                                                                                                                                                                                                                                                  | false       |
| **backendHardware** | reserved                                                             | -                                                                                                                                                                                                                                                     |             |
| **workerThread**    | use Worker                                                           | bool                                                                                                                                                                                                                                                  | true        |
| **mouseCapture**    | lock the mouse in player window                                      | bool                                                                                                                                                                                                                                                  | false       |
| **server**          | ipx server                                                           | netherlands, newyork, singapore                                                                                                                                                                                                                       | netherlands |
| **room**            | ipx room                                                             | str                                                                                                                                                                                                                                                   |             |
| **fullScreen**      | auto enter fullscreen mode                                           | bool                                                                                                                                                                                                                                                  | false       |
| **onEvent**         | listener of js-dos events                                            | function                                                                                                                                                                                                                                              |             |

> All options are optional

## DosProps

DosProps is a properties that you can use to control player after creation, use it like this:

```Javascript
cost props = Dos(elem, options);
props.setFullScreen(true); // switch to fullscreen mode
```

| property             | description                     | arguments       |
|----------------------|---------------------------------|-----------------|
| **setTheme**         | switch player theme             | theme name      |
| **setLang**          | change language                 | en, ru          |
| **setBackend**       | change backend                  | dosbox, dosboxX |
| **setBackendLocked** | change is backend locked or not | bool            |
| **setWorkerThread**  | select execution mode           | bool            |
| **setMouseCapture**  | set is mouse captured or not    | bool            |
| **setServer**        | change ipx server               | str             |
| **setRoom**          | change ipx room                 | str             |
| **setFrame**         | open named sidebar panel        | network         |
| **setBackground**    | change background image         | URL             |
| **setFullScreen      | change fullscreen mode          | bool            |

## Events

You can listen js-dos event by providing listener in Dos function:

```Javascript
Dos(elem, {
    onEvent: (event , ci?: [[[CommandInterface|command-interface.md]]]) => {
       console.log("js-dos event", event); 
    }
});
```
                    
| order | event         | description                                                                       | args                                     |
|:------|---------------|-----------------------------------------------------------------------------------|:-----------------------------------------|
| 0     | **emu-ready** | when emulators are fully loaded and ready to run the program                      |                                          |
| 1     | **bnd-play**  | when play button is clicked                                                       |                                          |
| 2     | **ci-ready**  | when backend is started and [CommandInterface](command-interface.md) is available | [CommandInterface](command-interface.md) |



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
