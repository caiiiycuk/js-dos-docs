# 3Dfx

js-dos can run 3Dfx games in browser with hardware-accelerated rendering. The emulation uses DOSBox-X Voodoo support,
and js-dos connects it to the browser through WebGL.

Use this mode for DOS or Windows 9x games that support 3Dfx/Glide rendering.

DOS games usually include the needed Glide runtime with the game. Windows 9x games need a 3Dfx driver installed inside
the guest OS.

## Requirements

3Dfx acceleration needs:

* DOSBox-X backend
* WebGL renderer
* OffscreenCanvas rendering, so the backend can own the WebGL context
* Voodoo enabled in `dosbox.conf`
* 3Dfx drivers installed inside Windows 9x, if you run a Windows game

> The regular DOSBox backend does not provide this mode. Use `dosboxX`.
>
{style="note"}

## Player options

Configure the player to start DOSBox-X with WebGL and OffscreenCanvas:

```Javascript
Dos(document.getElementById("dos"), {
    url: "game.jsdos",
    backend: "dosboxX",
    backendLocked: true,
    renderBackend: "webgl",
    offscreenCanvas: true,
    workerThread: true,
});
```

`offscreenCanvas: true` is important. Without it, the browser page can still use WebGL to draw normal DOS frames, but
the DOSBox-X backend will not receive the WebGL context required for accelerated Voodoo rendering.

## DOSBox-X config

Enable the Voodoo card in `dosbox.conf`:

```ini
[voodoo]
voodoo_card=opengl
```

`voodoo_card=opengl` selects the accelerated path used by js-dos. Keep `glide=false` for browser builds: js-dos uses
internal Voodoo emulation rendered through WebGL, not a native host Glide wrapper.

For Windows 9x bundles, keep the regular DOSBox-X Windows settings as well. A minimal useful base is:

```ini
[dosbox]
memsize=128

[cpu]
cputype=pentium_mmx
core=auto
integration device=true

[video]
vmemsize=8
vesa modelist width limit=0
vesa modelist height limit=0

[voodoo]
voodoo_card=opengl
```

## Windows 9x drivers

Windows 9x games need a 3Dfx driver installed in the guest OS. You can use the same workflow as any other Windows
driver:

1. Upload the extracted 3Dfx driver folder to the bundle.
2. Mount the uploaded files before booting Windows, for example with `mount d: .`.
3. Boot Windows 95/98.
4. Open Device Manager and update the Voodoo/PCI Multimedia Device driver from drive `D:`.
5. Reboot Windows.

For games that require DirectX, install the DirectX version expected by the game after the 3Dfx driver is installed.
See [Install OS](install-os.md#install-3dfx-driver-windows-9x) for the detailed Windows 9x driver steps.

## Verify acceleration

Open the player stats panel while the game is running. `Glide GL (3D)` should show `Yes`. The browser console may also contain
`glfx enabled` when DOSBox-X initializes the WebGL Voodoo path.

![Glige GL](3dfx.jpg)

If `Glide GL (3D)` is `No`, check these items first:

* the bundle uses `backend: "dosboxX"`
* `offscreenCanvas` is enabled and supported by the browser
* `renderBackend` is `webgl`
* `dosbox.conf` contains `[voodoo]` with `voodoo_card=opengl`
* the page is running in a browser with WebGL enabled

## Performance tips

Large Windows 9x and 3Dfx games are good candidates for [Sockdrive](jsdos-bundle.md), because disk images can be too
large to ship as one bundle. When publishing sockdrive data, serve it compressed with Brotli or gzip and use a CDN when
possible.

Use the turbo panel to tune CPU speed, cycles, fast forward, and frame skip. Some games are more stable with fixed CPU
cycles than with automatic CPU adjustment.
