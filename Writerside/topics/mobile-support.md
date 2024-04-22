# Mobile support (v7)

> js-dos v8 mobile support uses brand new mobile controls, they are still in development. Use **v7** if you need
> mobile support just now.
> 
{style="warning"}

js-dos v7 is mobile friendly. We build a set of controls that helps map touch gestures to key or mouse events.

The core concept of mobile support is a **layer abstraction**. Layer is a combination of virtual controls like buttons, 
joysticks and other that put on top of the game. Each control translates user interaction into some control sequences
(keys, mouse) for the game.

You can put on top as many layers as you want. However, only one layer can be active at the same time.

Using [game-studio](https://dos.zone/studio) can help to visually create this controls for game.

Take a look to our mobile tutorial:

<video src="https://www.youtube.com/watch?v=hULc-RsZXow" />

## Positioning

Layer do the layouting of controls that it have. When you create a layer you should choose the layout system.
Currently supported: `square` and `honeycomb` layout.

![grid.jpg](grid.jpg)

## Key Control

`Key` is a virtual button, that map touches into keyboard press and release events.

![key.jpg](key.jpg)

You must provide symbol and mapping key code for this control.

## Keyboard Control

`Keyboard` is a virtual button that toggles virtual keyboard when you press it.

![keyboard.jpg](keyboard.jpg)

You can't customize `Keyboard` control yet.

## Switch Control

`Switch` is a virtual button that switch between layers when you press it.

You must provide symbol and layer name to switch for this control.
Using this control you can easily implements multi-layer virtual controls.

Often one layer is enough for a game, but sometimes the game has a complex UI that requires changing layers between scenes. In that case you can attach multiple layers to the game and then switch between them when needed.

X-Com one such game, take a look how it works (`>` is a switch control).

<video src="https://www.youtube.com/watch?v=gu8uFM7yuls" />

## Screen Move Control

`Screen Move` is a virtual button that moves the mouse into one of the sides (up, down, left, right) or into one of the corners. Using this control you can support the scroll of the game screen in games that scroll when the mouse pointer is near the screen edge.

You should provide side to that control. Usually this control used in combination with `Nipple Activator`. 

## Nipple Activator Control

`Nipple Activator` is a joystick that activates neighboring controls when a joystick touches them.

![nipple-activator.jpg](nipple-activator.jpg)

This can be used to implement scroll behaviour in games. Watch the video above to see how it works.

You can't customize `Nipple Activator` control yet.

## Pointer Button Control

`Pointer Button` is a virtual button that changes the behaviour of click/tap. By default, click/tap is interpreted as left mouse button click. With this control you can change it to right button peramnently or temporary (while holding it).

This control has a `click` property, if it is set, then clicking on the virtual button will set the pointer button to the right mouse button for next game click/tap. In the other case the button will be set to the right mouse button while you are holding the virtual button.

Look the video above to see how it works (look on `R` virtual button).

## Pointer Move Control

`Pointer Move` is a virtual button that moves mouse pointer into special screen position.
Position is passed in range **[0 .. 1]** (e.g. [0.5, 0,5] is a screen center).

## Pointer Reset Control

`Pointer Reset` is a virtual button that force sycning of browser cursor position and in-game cursor position.

You can't customize `Pointer Reset` control yet.

## Options Control

Control type `Options` will create *Options* button that includes
* **toggle keyboard** action
* **save** action
* **toggle fullscreen** action

![special-button.jpg](special-button.jpg)

You can't customize `Options` control yet.