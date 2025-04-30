# MS-DOS Sockdrive (2GB)

## Creating bundle with MS-DOS game (using Sockdrive)

**Tutorial Video:**

<video src="https://www.youtube.com/watch?v=4pgorw5V91E" />

To create a bundle, you need to perform the following steps:

### 1. Open Studio and download base image

First, open [Game Studio](https://v8.js-dos.com/studio) and then 
press on **DOS v7.1** to download `MS-DOS 7.1` base image.

Now load base image using **Load button**.
![Load button](loadbutton.png)
{style="block"}

### 2. Run emulator and add program files

Press the "Run" button.
![The run button](the-run-button.jpg)
{style="block"}

When the emulator is started, open the File System panel using the disk icon.
Use upload file, or upload folder button to add your files to bundle File System.

![Upload files](upload-files.jpg)

### 3. Boot from base image and copy game files using Volkov Commander

Mount file system with game files to drive D, type:
```sh
mount d .
```

To boot from base image type in console:
```sh
boot c:
```

When ms-dos finish loading start Volkov Commander using this command:
```sh
vc\vc.com
```

Copy all files needed by game from disk D: to disk C:

![Volkov Commander](vc.jpg)

Now edit `C:\autoexec.bat` with Volkov Commander (F4 key). Add game executable
to the end of this file.

Exit Volkov Commander with F10 key.

### 3. Prepare js-dos bundle

Please delete all temporary files using `trash` button:

![Remove files](remove-files.jpg)

Restart emulation and add `boot c:` to DOSBox `[autoexec]` section.

![Restart](restart.png)

Setup autoexec:
![boot c:](bootc.png)
{style="block"}

### 4. Test bundle and export it

Run emulation, test it and then export using download button.

![Download bundle](download-bundle.jpg)

When a bundle is ready, the browser will prompt you to save it to your computer.

## Publish Sockdrive bundle

> Do not publish this bundle as is — it is too large and does not support save/load functionality. 
> 
{style="warning"}

Follow [Publish Sockdrive](Publish-Sockdrive-bundle.md) tutorial.