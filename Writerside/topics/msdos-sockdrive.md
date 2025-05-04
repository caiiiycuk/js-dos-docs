# MS-DOS Sockdrive (2GB)

## Creating bundle with MS-DOS game (using Sockdrive)

**Tutorial Video:**

<video src="https://www.youtube.com/watch?v=4pgorw5V91E" />

To create a bundle, you need to perform the following steps:

### 1. Open Studio and download base image

First, open [Game Studio](https://v8.js-dos.com/studio) and then 
press **DOS v7.1** to download the `MS-DOS 7.1` base image.

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


## CD-ROM support

Some games require CD-ROM emulation to start. Sometimes, you can simply use the SUBST command. You can mount a folder
to a drive letter, like this:

```sh
SUBST D: C:\CD
```

Then you can access drive `D:`, which will be mapped to the given folder. However, some games can check if this drive is
actually a CD-ROM or not. In that case, you can use FakeCd or FakeDr.

Here you can download [FakeCD](https://cdn.dos.zone/custom/tools/fakecd0c.zip). This archive contains a doc file with
documentation, but in the simplest case, you can use it like this:

```sh
fakecd C:\CD /l:d
```

This command will map folder `CD` to drive `D:` using a driver. This works in most cases. If not, you can try to use
the even more powerful FakeDr - you can get it and read the documentation on the [program website](https://www.math.uni-rostock.de/~nfa506/fakecddr.html).

