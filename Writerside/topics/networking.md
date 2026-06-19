# Networking

Some DOS games offer multiplayer gaming based on IPX, modem, or direct connection.
Using a browser, it's not possible to directly connect two clients. However, using js-dos cloud,
you can emulate direct connection.

> Only IPX protocol is supported right now.

Here is a tutorial video on how to use networking with js-dos:

<video src="https://www.youtube.com/watch?v=YH22lZ1EUjM"/>

By default, users are able to enjoy these games using the js-dos networking service.

> You need to enable networking features in player using [options](Player-API.md#options)

## Deploy your own networking backend

As mentioned, js-dos provides a public networking service, but for lower latency or custom deployments you can use your
own peer server.

> If your deployment is permanent and well maintained, then please let us know, we will add it to
> js-dos player defaults
> 
{style="note"}

### The Server

The server code is licensed under GPL-2 and you can find it [here](https://github.com/caiiiycuk/dosbox-ipx-server).
This server works as a relay, connecting multiple js-dos clients together. You need to deploy it on a publicly available
server.

1. Download the server [binary](https://github.com/caiiiycuk/dosbox-ipx-server/releases/)
2. `chmod +x server` to make it executable
3. Run it as follows:

   ```
   ./server -c cert.pem -k privkey.pem
   ```
   
   | Argument        | Description              |
   |-----------------|--------------------------|
   | ./server        | server binary            |
   | -c cert.pem     | path to SSL certificate  |
   | -k privkey.pem  | path to private key file |

### Configuring js-dos player

When the server is started, you can use it with js-dos player.
For example, if your peer server is available at `https://net.example.com`, [js-dos configuration](Player-API.md) will be:

```javascript
const params = new URLSearchParams(location.search);
// ...
Dos(el, {
    url: "game.jsdos",
    startIpxServer: params.get("server") === "1",
    connectIpxAddress: params.get("connect") ?? undefined,
    net: {
        peerServer: "https://net.example.com",
        token: "js-dos",
        secret: "fallback",
        iceServers: async () => [],
    },
});
```

Use `startIpxServer` on the host player, then pass the host peer ID or registered alias to another player through
`connectIpxAddress`.

## Reusing a network instance

By default, each player creates its own WebRTC networking instance when `startIpxServer` or `connectIpxAddress` is used.
If you run several players on the same page, you can pass the same `NetInstance` through `sharedNet`.

```Javascript
Dos(hostElement, {
    url: "game.jsdos",
    startIpxServer: true,
    onEvent: (event, ci) => {
        if (event === "ci-ready") {
            const sharedNet = ci.net();
            if (sharedNet === null) {
                return;
            }

            Dos(clientElement, {
                url: "game.jsdos",
                connectIpxAddress: String(sharedNet.peerId),
                sharedNet,
            });
        }
    },
});
```

`sharedNet` must be an object returned by `ci.net()` or an object implementing the same interface:

```Typescript
type NetInstance = {
    peerId: number;
    connected: Set<number>;
    wait: (ms: number) => void;
    registerAlias: (alias: string) => Promise<void>;
    unregisterAlias: (alias: string) => void;
    queryAliases: (query: string) => Promise<{ peerId: number }[]>;
    sendBinary: (data: Uint8Array, peerId: number) => number;
    recvBinary: () => { data: Uint8Array, peerId: number } | null;
    disconnect: (peerId: number) => void;
    shutdown: () => void;
}
```

You can register a readable alias for the current peer and then connect another player by that alias:

```Javascript
const net = ci.net();

if (net !== null) {
    await net.registerAlias("my-room");
}
```

Then pass `"my-room"` as `connectIpxAddress`.
