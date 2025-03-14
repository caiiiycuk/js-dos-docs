# Bundle creation

> If you are looking way to run your favorite game in browser, but do not want to develop website,
> then please create bundle for it using this guide, and send request to publis it on [dos.zone](doszone.md)
> 
{style="note"}

**Starting from js-dos v7**, the API expects a `js-dos bundle` that already contains all configuration needed to start the DOS program.
`js-dos bundle` is just a **ZIP** archive that contains the game itself and a js-dos configuration file (same as dosbox.conf file). 
For example, [digger.jsdos](https://dos.zone/digger-may-06-1999/) contains: 
```
.jsdos/dosbox.conf - dosbox configuration file (required)
.jsdos/jsdos.json  - additional configuration (optional)
DIGGER.COM         - game file
```

> File `.jsdos/dosbox.conf` is required. js-dos would not start if this file does not exist.
> 
{style="warning"}

You can generate a `js-dos bundle` programmatically. But we recommend you use our [bundle generator](https://dos.zone/studio)

## How to create bundles for DOS and Windows 95

<tabs>
    <tab title="English">
       <strong>Tutorial Video:</strong>
       <video src="https://www.youtube.com/watch?v=erq29Tsd9U8" />
    </tab>
    <tab title="Russian">
      <strong>Обучающее видео:</strong>
      <video src="https://www.youtube.com/watch?v=PWugVpHx87M" />
      <strong>Как крафтить игры от NicCarter54:</strong>
      <video src="https://www.youtube.com/watch?v=7WfMcQt38c0" />
    </tab>
</tabs>

To create a bundle, you need to perform the following steps:

**1. Create an empty bundle and select dosbox configuration**

To do this, press on "Create empty bundle" link.
![Create empty bundle](create-empty-bundle.jpg)
{style="block"}

Now you need to select one of the available configurations or write your own.
![Select configuration](select-configuration.jpg)
{style="block"}

7.xx is the best option if your program is a plain DOS program. It is also compatible both with
js-dos v7 and v8. 

> While you do not need to install Windows program, please use **7.xx** configuration.
> 
{style="note"}

**2. Run emulator and add program files**

Press the "Run" button.
![The run button](the-run-button.jpg)
{style="block"}

When the emulator is started, open the File System panel using the disk icon.
Use upload file, or upload folder button to add your files to bundle File System.

![Upload files](upload-files.jpg)

**3. Export result as js-dos bundle**

To export use download button.

![Download bundle](download-bundle.jpg)

When a bundle is ready, the browser will prompt you to save it somewhere on your PC. 
Now when you have a bundle, go ahead and publish it somewhere.

> Due to the performance reasons js-dos archive created without compression.
> If you plan to publish it on your website, please repack it with maximum compression.
> 
{style="warning"}

> If you need to change the configuration of bundle in the middle, you can use the restart button. It 
> will redirect you back to the configuration editor.
> 
> ![Restart button](restart-button.jpg)
> 
{style="note"}

## Sockdrive bundles (Windows)

> It's recommended to use DOS Browser to make the process faster. [DOS Browser](https://dos.zone/download/) has a builtin acceleration
> for DOS.
> 

When you need to install Windows program, then you need to use [sockdrive](sockdrive.md). These drives
are located in js-dos cloud, and transfer only required data to browser. The way of creating the bundle
 is the same as described above. Except one thing: you need to [create](fork-drive.md) such a drive first. 

After creating a drive, you need to attach it to your configuration. Use "Browse" button for this, or
enter drive manually.

![Browse button](browser-button.jpg)

Now you can start emulator and create bundle.

> Do not add files to bundle. You must copy them to your Windows installation.
> To do this. **Create a bundle first and then use it to boot.** 
> 
{style="warning"}

Now reload the page and load your bundle with load button.

![Load bundle](load-bundle.jpg)

When emulator is started, add files as explained before and mount bundle FS as some drive:

```
mount d .
```

Boot Windows and copy files to it. They will be immidiately stored in a js-dos cloud.
So any next boot will have them no need to reexport a bundle.


## Advanced configuration

### .jsdos/dosbox.conf

> File `.jsdos/dosbox.conf` is required. js-dos would not start if this file does not exist.

This file is a regular [dosbox configuration](https://www.dosbox.com/wiki/Dosbox.conf). Not
all features are supported, but we will work on it.

### .jsdos/jsdos.json

This file contains additional configuration that does not exist in the dosbox configuration file.
For example, it's used to configure [virtual controls](mobile-support-v7.md). If you used game studio
to create bundles then it will also contain all information from `dosbox.conf`. And it looks like:

```json
{
  "layers": [
    {
      "grid": "honeycomb",
      "title": "Layer#0",
      "controls": [
        {
          "row": 0
```

This file can contain any configuration that you want. You can access it with [Command Interface](command-interface.md).
For example:
```Javascript
const ci = await Dos(/*element*/).run(/*bundle url*/);
const config = await ci.config();
console.log(config.layers);
```

This snippet will print information about gestures that config has. It's a very powerful feature, it can be used
to add new optional features to js-dos. 

## Game Studio

Game Studio is a recommended tool for creating `js-dos bundles`.

`js-dos bundles` generated with a game studio always support the latest features that js-dos have. It generates configuration files for you.

Bundle generated by game studio is not licensed, you can use them whatever you want.

<tabs>
<tab title="v8">
Open <a href="https://dos.zone/studio-v8">Game Studio v8</a>
</tab>
<tab title="v7">
Open <a href="https://dos.zone/studio">Game Studio v7</a>

> Bundles are compatible with v8
>
>
{style="note"}
</tab>
</tabs>
