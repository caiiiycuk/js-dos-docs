# Windows 9x

> Do not publish sockdrive bundle as is — they are large and do not support save/load functionality.
>
> Follow **[Publish Sockdrive](Publish-Sockdrive-bundle.md)** tutorial instead.
>
{style="warning"}

## Creating bundle with Windows 9x game (using sockdrive)

**Tutorial Video:**

<video src="https://www.youtube.com/watch?v=ZQDFJ-n1BgQ" />

### 1. Open Studio and download base image

First, open [Game Studio](https://v8.js-dos.com/studio) and then
download one of windows base images (Win 95, Win 98, etc.)

Now load the base image using the **Load button**.
![Load button](loadbutton.png)
{style="block"}

### 2. Run emulator and add program files

Press the "Run" button.
![The run button](the-run-button.jpg)
{style="block"}

When the emulator starts, open the File System panel using the disk icon.
Use the upload file or upload folder button to add your files to the bundle File System.

![Upload files](upload-files.jpg)

### 3. Mount uploaded files as drive D:

When files uploaded, you can:

1) Mount whole disk as drive D:

```
mount d .
```

2) Mount cdrom image (iso, mdf, etc.) as drive D:

```
imgmount d cd.iso
```


### 4. Boot the windows and install the game

To boot Windows type:

```
boot c:
```

Now, when OS is started install game as usual.

### 5. Prepare js-dos bundle

Please delete all temporary files using `trash` button:

![Remove files](remove-files.jpg)

Restart emulation and add `boot c:` to DOSBox `[autoexec]` section.

![Restart](restart.png)

Setup autoexec:
![boot c:](bootc.png)
{style="block"}

### 5. Test bundle and export it

Run emulation, test it and then export using download button.

![Download bundle](download-bundle.jpg)

When a bundle is ready, the browser will prompt you to save it to your computer.

## CD-ROM support

The best option to emulate CD-ROM in Windows is to use Daemon tools. Some base images
already have it, but if not you can install it manually.

1) Install [XUSBSUPP.EXE](https://cdn.dos.zone/custom/tools/XUSBSUPP.EXE), not needed for Win 98.
2) Reboot OS
3) Install [daemon tools](https://cdn.dos.zone/custom/tools/daemon347.exe)

## MMX issues (random crashes)

By default, our DOSBox configuration enables emulation of MMX instructions. If your game does not run stably 
or experiences random crashes, it is very likely due to MMX instructions. Try disabling them and retest your game.
To disable MMX, update your dosbox.conf as follows:

```
[cpu]
cputype=pentium
```

## Driver crashes

Sometimes a game can crash due to an incompatible driver. You can try installing a different 3dfx or video driver 
— this often helps.

## Pointer lock (mouse capture)

By default, dosbox-x + win9x works fine without mouse capture, but if you need it, it is also supported. 
To enable it follow this steps:

1. Start system without mouse capture
2. Install this mouse driver: [intellipoint2_2.zip](https://github.com/user-attachments/files/21042227/intellipoint2_2.zip)
3. in dosbox.conf change emulation mode to `mouse_emulation=always`
4. in device config enable standard mouse driver

![Image](https://github.com/user-attachments/assets/8308dec3-36d1-4b13-bc58-4b0d79be2989)

After reboot mouse capture will work like a charm. 

For example, you can try it [here](https://dos.zone/settlers3/)

## Useful files

* [3Dfx driver](https://cdn.dos.zone/custom/tools/win98/3dfx_3.01.00.zip)
* [DirectX 7](https://cdn.dos.zone/custom/tools/win98/dx7.zip)
* [DirectX 8](https://cdn.dos.zone/custom/tools/win98/dx8.zip)
* [DirectX 9](https://cdn.dos.zone/custom/tools/win98/dx9.zip)
* [MSI Installer v2](https://cdn.dos.zone/custom/tools/win98/inst2.exe)
