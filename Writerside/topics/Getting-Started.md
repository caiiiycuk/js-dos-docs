# Getting Started (sockdrive)

Typically, js-dos [bundle](jsdos-bundle.md) contains everything needed to run DOS program in a browser,
including game files. This creates a straightforward and effective way to run and share dos programs.

> While your bundle is relatively small, you [**should use default bundle integration**](dos-api.md). It is much faster
> and works without a cloud.
> 
> Try to compress your bundle with Brotli this can help to reduce it significantly. 
> 
> sockdrive is not supported by **DOSBox** (not -X) backend.
> 
{style="warning"}

Some games, especially designed for Windows, can be huge. They easily take up to gigabytes on HDD drive.
In that case, packaging game files into a bundle is no sense, resulting bundle will be big to download 
and can reach the memory limit of browser in runtime.

Especially for such integrations, sockdrive was designed.

## Remote HDD drives

sockdrive is like a remote hdd drive for DOSBox. You can connect it to DOSBox-X with command:

```
imgmount N sockdrive wss://sockdrive.js-dos.com:8001 <owner> <drive>
```

* **N** - is a drive number (2 is C:, 3 is D:).
* **sockdrive** - is a keyword, must present, otherwise `imgmount` will not understand the command
* **wss://...** - address of sockdrive backend
* **owner** - is a token of drive owner (usually the same as an email that was used for subscription)
* **drive** - is a drive name

## Diablo I in browser

js-dos player with sockdrive integration is absolutely the same as for bundles. But instead of mounting
FS as drive C:, you should mount sockdrive. In the case of diablo:

```Typescript
[autoexec]
echo off
imgmount 2 sockdrive wss://sockdrive.js-dos.com:8001 dos.zone diablo_109_c
imgmount 3 sockdrive wss://sockdrive.js-dos.com:8001 dos.zone diablo_109_d
boot c:
```

As you can see, we:
1. Mount drive dos.zone/diablo_109_c to C:
2. Mount drive dos.zone/diablo_109_d to D:
3. `boot c:`, this is DOSBox-X command to start boot from one of the provided drives

Save the following code somewhere to test locally: 
```Typescript
```
{src="examples/diablo.html" collapsible="true" collapsed-title="Full page source"}

If you run this snippet in browser, you will be able to play Diablo I game.

![Diablo I in the Browser](diablo-i.jpg)

## Read access

Everybody even anonymous users can use js-dos with attached sockdrives. 

## Write access

Only users with active subscription will be able to write into remote hdd and store progress, as well as
create new hdd drives.

> Only the owner of a drive can directly write to it; for other users, the drive is forked internally. 
