# Installing Windows 98

> Typically, you don't need to do this. Just fork one of [system drives](System-images.md). 

## Seamless mouse integration

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

## Install 3Dfx driver

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

## DirectX 9.0

1. Download [DirectX 9.0c](https://cdn.dos.zone/custom/tools/win98/dx9.zip), extract it to some folder.
2. Extract it somewhere
3. Upload this folder to js-dos before windows boot
4. Mount FS as drive D:
   ```Bash
   mount d: .
   ```

When windows started run the **D:\Setup.exe**.

## Windows Installer 2.0

Download [Windows Installer 2.0](https://cdn.dos.zone/custom/tools/win98/inst2.exe) and follow same steps as for DirectX 9.0
