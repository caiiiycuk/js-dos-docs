# Command Interface (CI)

The `Command Interface` is the object that allows you to communicate with a js-dos instance.
Once you run a [js-dos bundle](jsdos-bundle.md), you will receive a `Command Interface` instance.

<tabs>
    <tab title="v8">
        <code-block lang="typescript">
            Dos(/*element*/, {
                url: /* bundle url */,
                onEvent: (event, ci: CommandInterface) => {
                    if (event === "ci-ready") {
                        // now ci is ready
                    }
                },
            });
        </code-block>
    </tab>
    <tab title="v7">
        <code-block lang="javascript">
            const ci = await Dos(/*element*/).run(/*bundle url*/);
        </code-block>
    </tab>
</tabs>

CI interface:

```Typescript
export interface CommandInterface {
    config: () => Promise<DosConfig>;
    height: () => number;
    width: () => number;
    soundFrequency: () => number;
    screenshot: () => Promise<ImageData>;
    pause: () => void;
    resume: () => void;
    mute: () => void;
    unmute: () => void;
    exit: () => Promise<void>;
    simulateKeyPress: (...keyCodes: number[]) => void;
    sendKeyEvent: (keyCode: number, pressed: boolean) => void;
    sendMouseMotion: (x: number, y: number) => void;
    sendMouseRelativeMotion: (x: number, y: number) => void;
    sendMouseButton: (button: number, pressed: boolean) => void;
    sendMouseSync: () => void;
    sendBackendEvent: (event: any) => void;
    persist(onlyChanges?: boolean): Promise<Uint8Array | null>;
    events(): CommandInterfaceEvents;
    net(): Net | null;
    networkConnect(networkType: NetworkType, address: string): Promise<void>;
    networkDisconnect(networkType: NetworkType): Promise<void>;
    asyncifyStats(): Promise<AsyncifyStats>;
    fsTree(): Promise<FsNode>;
    fsReadFile(file: string): Promise<Uint8Array>;
    fsWriteFile(file: string, contents: ReadableStream<Uint8Array> | Uint8Array): Promise<void>;
    fsDeleteFile(file: string): Promise<boolean>;
    getRunningProgram(): Promise<string>;
}
```

Events interface:
```Typescript
export type MessageType = "log" | "warn" | "error" | string;

export interface CommandInterfaceEvents {
    onStdout: (consumer: (message: string) => void) => void;
    onFrameSize: (consumer: (width: number, height: number) => void) => void;
    onFrame: (consumer: (rgb: Uint8Array | null, rgba: Uint8Array | null) => void) => void;
    onSoundPush: (consumer: (samples: Float32Array) => void) => void;
    onExit: (consumer: () => void) => void;
    onMessage: (consumer: (msgType: MessageType, ...args: any[]) => void) => void;
    onNetworkConnected: (consumer: (networkType: NetworkType, address: string) => void) => void;
    onNetworkDisconnected: (consumer: (networkType: NetworkType) => void) => void;
    onUnload: (consumer: () => Promise<void>) => void;
}
```

## Runtime control

Use these methods to control a running backend:

```Javascript
ci.pause();
ci.resume();

ci.mute();
ci.unmute();

await ci.exit();
```

`pause()` stops emulation and mutes sound. `resume()` continues it. `exit()` stops the backend and releases its resources.

You can also get current runtime state:

```Javascript
const config = await ci.config();
const width = ci.width();
const height = ci.height();
const frequency = ci.soundFrequency();
const image = await ci.screenshot();
const program = await ci.getRunningProgram();
```

`screenshot()` returns `ImageData` for the latest frame. `getRunningProgram()` returns the current DOS program name reported
by the backend.

## Input

Keyboard methods use js-dos key codes. For common Latin letters and digits the codes match ASCII values.
Special keys use the same numeric values as the virtual keyboard configuration, for example Enter is `257`,
Left arrow is `263`, Up arrow is `265`, and Escape is `256`.

```Javascript
// Press and release Enter.
ci.simulateKeyPress(257);

// Hold Ctrl+C, then release it.
ci.sendKeyEvent(341, true);
ci.sendKeyEvent(67, true);
ci.sendKeyEvent(67, false);
ci.sendKeyEvent(341, false);
```

Mouse coordinates for `sendMouseMotion` are normalized to the `[0, 1]` range:

```Javascript
ci.sendMouseMotion(0.5, 0.5);
ci.sendMouseRelativeMotion(10, -4);
ci.sendMouseButton(0, true);
ci.sendMouseButton(0, false);
ci.sendMouseSync();
```

## Frames and sound

The backend emits frame and sound data through events:

```Javascript
ci.events().onFrameSize((width, height) => {
    console.log("frame size", width, height);
});

ci.events().onFrame((rgb, rgba) => {
    const frame = rgb ?? rgba;
    if (frame === null) {
        return;
    }

    // rgb has width * height * 3 bytes.
    // rgba has width * height * 4 bytes.
});

ci.events().onSoundPush((samples) => {
    // samples is Float32Array with audio frames from the backend
});
```

## Backend events

`sendBackendEvent()` sends a low-level backend event. It is mainly useful for features that are implemented by a specific
backend.

```Javascript
ci.sendBackendEvent({
    type: "wc-trigger-event",
    event: "hand_savestate",
});

ci.sendBackendEvent({
    type: "wc-trigger-event",
    event: "hand_loadstate",
});
```

Supported built-in trigger events include:

| event                 | backend             | description        |
|-----------------------|---------------------|--------------------|
| **hand_savestate**    | DOSBox-X            | Save emulator state |
| **hand_loadstate**    | DOSBox-X            | Load emulator state |
| **hand_ipx_startserver** | DOSBox, DOSBox-X | Start IPX server    |

## Networking

Use `networkConnect()` and `networkDisconnect()` with `NetworkType.NETWORK_DOSBOX_IPX` to control IPX networking from code.

```Typescript
await ci.networkConnect(NetworkType.NETWORK_DOSBOX_IPX, "12345");

ci.events().onNetworkConnected((networkType, address) => {
    console.log("connected", networkType, address);
});

ci.events().onNetworkDisconnected((networkType) => {
    console.log("disconnected", networkType);
});

await ci.networkDisconnect(NetworkType.NETWORK_DOSBOX_IPX);
```

`ci.net()` returns the low-level WebRTC networking object when networking was enabled for this backend, otherwise it
returns `null`.

## Diagnostics

`asyncifyStats()` returns runtime counters that are useful for performance overlays and debugging:

```Javascript
const stats = await ci.asyncifyStats();
console.log(stats.messageFrame, stats.messageSound, stats.cpuMetrics);
```

You can subscribe to backend logs with `onMessage()`:

```Javascript
ci.events().onMessage((type, ...args) => {
    console.log(type, ...args);
});
```

`onUnload()` lets you delay backend unload until your cleanup promise is resolved:

```Javascript
ci.events().onUnload(async () => {
    await saveExternalState();
});
```
