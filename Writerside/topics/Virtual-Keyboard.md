# Virtual Keyboard

js-dos v8 provides virtual/soft keyboard for mobile devices.
This keyboard is highly customizable. You can control it by changing layout and symbols.

## Layout

Property **softKeyboardLayout** configures layout of keyboard, it's an array of string. Each item in that array
is a line of keys, e.g.:

```Javascript
  softKeyboardLayout: [
    "q w e r t",
    "{up} {down}",
    "{left} {right}",
    "{layout}"
  ],
```

Will define the following keyboard:

![](softKeyboard-1.jpg)

You also can group buttons in the keyboard with an array of arrays.

```Javascript
  softKeyboardLayout: [
    [
      [
        "{up}",
      ],
      [
        "{left} {right}",
      ],
      [
        "{down}",
      ]
    ],
  ],
```

Will define the following keyboard:

![](softkeyboard-2.jpg)

You can provide multiple layouts and switch between them with special `{layout}` button:

```Javascript
  softkeyboardlayout: [
    [
      [
        "{layout} {up}",
      ],
    ],
    [
      [
        "{layout} {down}",
      ],
    ],
  ],
```

The result will be the following:

| 1st layout             | 2nd layout             |
|------------------------|------------------------|
| ![](softkeyboard-3.jpg) | ![](softkeyboard-4.jpg) |


## Symbols

Property **softKeyboardSymbols** configures how each key will be rendered, it's an array of mapping layout symbols.
For example, we can use the same layout for RU and EN keyboards:

```Javascript
softKeyboardSymbols: [
  {
    "{up}": "↑",
    "{down}": "↓",
    "{left}": "←",
    "{right}": "→",
  },
  {
    "{up}": "↑",
    "{down}": "↓",
    "{left}": "←",
    "{right}": "→",
    "q": "й", "w": "ц", "e": "у", "r": "к", "t": "е",
  },
],
```

The result will be the following:

| EN                      | RU                      |
|-------------------------|-------------------------|
| ![](softKeyboard-1.jpg) | ![](softKeyboard-2.jpg) |

As you can see, symbols overrides view of keys, you can provide as many symbol maps as you want. 
They are cycled by pressing special key `{symbols}`.

## Special keys

The following keys have a special meaning when defined in layout:

|                    |                |
|--------------------|----------------|
| `{layout}`         | Next layout    |
| `{symbols}`        | Next symbols   |
| `{enter}`          | Enter          |
| `{shift}`          | Left Shift     |
| `{bksp}`           | Backspace      |
| `{lock}`           | Capslock       |
| `{tab}`            | Tab            |
| `{space}`          | Space          |
| `{esc}`            | Escape         |
| `{ctrl}`           | Left Control   |
| `{alt}`            | Left Alt       |
| `{up}`             | Up Arrow       |
| `{down}`           | Down Arrow     |
| `{left}`           | Left Arrow     |
| `{right}`          | Right Arrow    |
| `{f1}` .. `{f12}`  | F1 .. F12 keys |
| `{kp0}` .. `{kp9}` | Keypad keys    |
| `{pgup}`           | Page Up        |
| `{pgdown}`         | Page Down      |
| `{home}`           | Home           |
| `{end}`            | End            |