# Install OS

> Typically, you don't need to do this. Just use one of the pre-created images available in [Game Studio](https://v8.js-dos.com/studio)
>
{style="warning"}

The installation process is similar for all operating systems. You can use these instructions not only for
MS-DOS/Windows. The installation process includes:

1. Download an empty disk image
2. Set up the configuration of DOSBox
3. Create a js-dos bundle
4. Get the installation files and a bootable diskette or bootable CD
5. Upload these images to the file system
6. Boot from the CD or diskette
7. Install the operating system

## Download the empty disk image

Currently, js-dos provides two disk images:

* [FAT16 256MB](https://br.cdn.dos.zone/js-dos/system/system-fat16-256m.qcow2) – a good choice for small installations (MS-DOS, Win 3.11)
* [FAT32 2GB](https://br.cdn.dos.zone/js-dos/system/system-fat32-2gb.qcow2) – the best option for Win 9x and installations that require more space

## How to boot with empty image 

An empty image is just a formatted drive without any data. Because it is empty, you can't boot from it. You need to have a bootable diskette to boot with the empty image.

1. First, you need to boot with the newly created drive. The easiest way to do this is to download a DOS bootable
diskette. You can get one [here](https://cdn.dos.zone/custom/tools/dos.img) (dos.img).

2. Open [studio](https://v8.js-dos.com/studio/) and press **Create empty bundle** link

3. Select a configured preset that fits your OS, or provide your own configuration
![select preset](preset.png)
{style="block"}

4. Start emulation by pressing **Play** button

5. Open FS sidebar, press upload file, and select `dos.img` and `qcow2` empty image.
![boot from A:](boot-from-a.jpg)
{style="block"}

6. Type `mount -u c:` to **unmount** drive C:
7. Type `imgmount a: dos.img` to mount the image as drive **A:**
8. Type `imgmount 2 <name-of-qcow2>.qcow2` to mount the qcow2 as drive **C:**
9. Now boot from **A:** by typing `boot a:`
10. When you have booted, you can change to drive **C:**. Do what you want: copy files, install OS, etc.
![drive C:](drive-c.jpg)
{style="block"}

## Install MS-DOS

Download the MS-DOS distribution and extract it to a location of your choice. Usually MS-DOS is distributed as a bunch of floppy disks - in our tutorial we used an installation with 3 diskettes. Now add all needed files to the emulator (MS-DOS distribution, qcow2 image and bootable diskette)

![add all files](ms-dos-boot.png)

Let's mount the emulator file system as drive **D:** - we need to extract installation files on it to use later.

```
mount d .
imgmount A disk01.img -U
xcopy A:\. D:\

imgmount A disk02.img -U
xcopy A:\. D:\

imgmount A disk03.img -U
xcopy A:\. D:\
```

By doing this, we have extracted the DOS distribution to the virtual file system. Now we can boot with our bootable diskette.
Please start DOS with CD-ROM support - in this case, you will have your system drive C: and drive D: will contain your virtual file system.

```
imgmount A dos.img -U
imgmount 2 system-fat32-2gb.qcow2
boot a:
```

When you have booted, change to drive **D:** and run setup using `SETUP.EXE`

```
D:
SETUP.EXE
```

Follow the setup instructions. When the installation is done, you can clean up your bundle using the Trash icon in the FS sidebar.

![cleanup](trash.png)

If you installed the system directly on drive C:, then you can add mount and boot commands to the autoexec section:

1. Restart emulation from the beginning
![restart emulation](restart.png)
{style="block"}

2. Setup autoexec
![boot c:](bootc.png)
{style="block"}

3. Run emulation to ensure that everything works fine

At the end, you can export the installed OS as a js-dos bundle.

![Export as js-dos](exportbundle.png)

## Tutorial video (MS-DOS 7.1)

<video src="https://www.youtube.com/watch?v=6D6-xjqL63s" />

## Install Win9x

Installing Windows 9x is very similar to installing MS-DOS 7.1 — you just need to use the correct Windows installation ISO instead of an MS-DOS one.

## Configure your installation

### Seamless mouse integration (windows 9x)

1. Download [binary drivers](https://github.com/joncampbell123/doslib/releases)
2. Mount **install.dsk** from *doslib\windrv\dosboxpi\bin* as A: drive
    ```Bash
    imgmount a: install.dsk
    ```
3. Boot your Windows 98 image
4. Open _Control Panel -> Add New Hardware -> Mouse -> Have Disk_
5. In the file selector, please select folder related to your Windows version, e.g., win98.
6. Install **DOSBox-X Mouse Pointer Integration**
7. Open _Control Panel -> System -> Device Manager_
8. Select **Standard PS/2 Port Mouse** and press _Properties_
9. Check **Disable in this hardware profile**
10. Reboot

### Install 3Dfx driver (windows 9x)

1. Download [3.01 Driver](https://cdn.dos.zone/custom/tools/win98/3dfx_3.01.00.zip)
2. Extract it somewhere
3. Upload this folder to js-dos before windows boot
4. Mount FS as drive D:
   ```Bash
   mount d: .
   ```
5. Boot Windows 98
6. Open _Device Manager Start -> Settings -> Control Panel -> System_
7. Locate the existing reference to the card it will be listed as:

       Reference Card: 
         Other Devices / PCI Multimedia Device or
         Sound, Video, Game Controllers / Voodoo2
         3Dfx Voodoo

8. Double-Click on the card, then click the driver Tab.
9. Click **Update Driver** then click the _Next Button_ twice on the Detection Dialog Box. Ensure that
   **Specify a Location is checked** and Microsoft Windows Update is not checked. Click Browse to continue.
10. Select drive **D:**
11. If windows asks please select **Install one of the other drivers**. Press Next.
12. Windows will copy the drivers. **Overwrite all files if windows asks.**
13. Reboot the system when prompted.

## DirectX (windows 9x)

1. Download [DirectX 6](https://cdn.dos.zone/custom/tools/win98/dx6.zip), [DirectX 7](https://cdn.dos.zone/custom/tools/win98/dx7.zip), [DirectX 8](https://cdn.dos.zone/custom/tools/win98/dx8.zip) or [DirectX 9.0c](https://cdn.dos.zone/custom/tools/win98/dx9.zip), extract it to some folder.
2. Extract it somewhere
3. Upload this folder to js-dos before Windows boot
4. Mount FS as drive D:
   ```Bash
   mount d: .
   ```

When Windows starts, run the **D:\Setup.exe**.

## Windows Installer 2.0 (windows 9x)

Download [Windows Installer 2.0](https://cdn.dos.zone/custom/tools/win98/inst2.exe) and follow same steps as for DirectX 9.0
