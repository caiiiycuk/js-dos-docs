# Pure JavaScript

js-dos v7/v8 is better than 6.22 in many cases. But it targets modern browsers.
It supports only wasm execution, so it's not possible to run js-dos in pure js mode.
In rare cases this can be important and js-dos 6.22 is only one option here.

However, [js-dos bundle](jsdos-bundle.md) is plain zip archive, so you can
use them also with js-dos 6.22.

```Javascript
    Dos(document.getElementById("jsdos"), {
        wdosboxUrl: "https://v8.js-dos.com/v6.22/dosbox.js",
    }).ready((fs, main) => {
        fs.extract("https://v8.js-dos.com/bundles/digger.jsdos").then(() => {
            main(["-conf", ".jsdos/dosbox.conf"]);
        });
    });
```


```html
{}
```
{src="examples/dos-6.22.html" collapsible="true" collapsed-title="Source code"}

## Documentation

[Documentation](https://js-dos.com/index_6.22.html) of js-dos 6.22.