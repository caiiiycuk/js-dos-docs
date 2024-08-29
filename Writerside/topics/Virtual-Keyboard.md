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
    "⎘"
  ],
```

Will define the following keyboard:

![](softKeyboard-1.jpg)

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
They are cycled by pressing special key **⎘**.

## Special keys

The following keys have a special meaning when defined in layout:

|           |                            |
|-----------|----------------------------|
| `{enter}` | Enter                      |
| `{shift}` | Left Shift                 |
| `{bksp}`  | Backspace                  |
| `{lock}`  | Capslock                   |
| `{tab}`   | Tab                        |
| `{space}` | Space                      |
| `{esc}`   | Escape                     |
| `{ctrl}`  | Left Control               |
| `{alt}`   | Left Alt                   |
| `{up}`    | Up Arrow                   |
| `{down}`  | Down Arrow                 |
| `{left}`  | Left Arrow                 |
| `{right}` | Right Arrow                |
| `,`       | Comma                      |
| `.`       | Period                     |
| `:`       | Left Shift + Semicolon     |
| `{`       | Left Shift + Left Bracket  |
| `}`       | Left Shift + Right Bracket |
| `⎘`       | Cycling symbols            |
