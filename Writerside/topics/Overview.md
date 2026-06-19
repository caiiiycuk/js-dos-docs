# js-dos

The simplest API to run **DOS/Win 9x** programs in browser or node.

js-dos is a frontend for [emulators](https://github.com/caiiiycuk/emulators) that provides nice UI and infrastructure
to run DOS or Windows programs in browser.

It provides full-featured **DOS player** that can be easily installed and used to get your DOS program up and running in
browser quickly. js-dos provides many advanced features like multiplayer, save/load, and large disk streaming.
All available features are enabled for any integration and free.

The unique capabilities:
* The only browser solution with accelerated [**3Dfx**](3Dfx.md) rendering through a WebGL-backed renderer
* Full [**IPX** networking](networking.md) stack in browser, powered by WebRTC-NET, with IPX server creation, direct
  peer/alias connections, and shared network instances
* **Windows 9x** support through DOSBox-X, including prebuilt Windows 95/98 system images and support for Windows games
  with DirectX and 3Dfx drivers
* **Sockdrive V2** for large disk images: backendless streaming drives, preload modes, and improved persistence handling
* Modern local persistence based on **OPFS**, with local filesystem changes, storage usage information, and `fsChanges`
  hooks for custom save storage

The key features:
* Works in **worker** or render thread
* Support execution in Node and Browsers
* Multiple backends: DOSBox, DOSBox-X
* Mobile support (v8 - WIP, v7 - production)
* Able to run huge games (like Diablo, etc.)
* Multiplayer support
* Save/load support with local persistence and custom storage integrations
* WebAssembly and pure JS versions
* AudioWorklet mode and experimental JSPI backend support
* [Turbo controls](turbo.md) with CPU metrics, speed, cycles, fast forward, and frame skip controls

> If you want to build custom DOS player, then please use [emulators](threejs.md) packages instead.
>
 
## Demo

* [DOS.Zone](https://dos.zone) - community portal with 1900+ dos games
* [3Dfx](https://dos.zone/3dfx/)
* [Multiplayer](https://dos.zone/mp)
* [NFS](https://dos.zone/the-need-for-speed-sep-1995/)
* [Age Of Empries II](https://dos.zone/age-of-empires2/)


## Support

| [Subscription / Подписка](https://cloudsdk.dev/)                                   | [Visa / MasterCard / МИР](https://pay.cloudtips.ru/p/894f907b) | [Buy Me A Coffee!](https://buymeacoffee.com/caiiiycuk) |
|-------------------------------------------------------------------------------------|-----------------------------------------------------------------|--------------------------------------------------------|
| <img src="https://dos.zone/assets/qr-jsdos.png" alt="js-dos qr code" width="128" /> | <img src="https://cdn.dos.zone/custom/qr/cloud_tips_256.png" alt="cloud tips qr code" width="128" /> | <img src="https://cdn.dos.zone/custom/qr/bmc_qr_256.png" alt="bmc qr code" width="128" /> |
| js-dos                                                                              | Cloud Tips                                                      | @caiiiycuk                                             |

| BTC | ETH |
|-----|-----|
| <img src="https://cdn.dos.zone/custom/qr/btc_pub_256.png" alt="btc qr code" width="128" /> | <img src="https://cdn.dos.zone/custom/qr/eth-qr_256.png" alt="eth qr code" width="128" /> |
| 1EngssY81sziuQyb9JGXURG3WDajyC8kW6 | 0x54FEBE29Cd700f88468032b33c33CdcD7c7cCa53 |
