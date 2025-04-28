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