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
