# Networking

Some DOS games offer multiplayer gaming based on IPX, modem, or direct connection.
Using a browser, it's not possible to directly connect two clients. However, using js-dos cloud,
you can emulate direct connection.

> Only IPX protocol is supported right now.
>

## Deploy your own networking backend

### How networking works

js-dos networking uses [WebRTC-NET](https://github.com/caiiiycuk/WebRTC-NET). The browser player loads
`webrtcnet.mjs` and `webrtcnet.wasm`, opens a WebSocket connection to a signaling server, and then creates WebRTC
connections between players.

The signaling server does not carry game traffic. It only:

1. assigns each player a `peerId`;
2. stores aliases registered by players;
3. forwards WebRTC signaling messages such as SDP offers, SDP answers, and ICE candidates.

After signaling is complete, DOS/IPX packets go through the WebRTC data channel between players. If direct peer-to-peer
connection is impossible because of NAT/firewall rules, WebRTC needs STUN/TURN servers from the ICE server list. TURN is
the relay for game traffic; the signaling server is not a TURN relay.

### Signaling server

Use the WebRTC-NET `peer-server` from [WebRTC-NET](https://github.com/caiiiycuk/WebRTC-NET/releases).

A production deployment usually has these public endpoints:

| endpoint               | role                                                                                 |
|------------------------|--------------------------------------------------------------------------------------|
| `wss://net.example.com/ws` | WebRTC-NET `peer-server`; exchanges `peerId`, aliases, SDP offers/answers, and ICE candidates. |
| `turn.example.com`     | STUN/TURN server; helps WebRTC discover routes and relays game traffic when needed.  |

The two roles may run on the same machine or on separate machines. They are configured separately in js-dos:
`net.peerServer` points to the signaling endpoint, while `net.iceServers` returns STUN/TURN entries.

Download the Linux `peer-server` binary from the
[WebRTC-NET Releases](https://github.com/caiiiycuk/WebRTC-NET/releases) section. Use the Linux x86_64 binary, then copy
it to your server and make it executable:

```bash
sudo mkdir -p /opt/webrtc-net
sudo install -m 755 peer-server.bin.x86_64 /opt/webrtc-net/peer-server.bin.x86_64
```

Run it with TLS so browsers can connect from HTTPS pages:

```bash
/opt/webrtc-net/peer-server.bin.x86_64 \
    --port 443 \
    --tls \
    --tls-cert /etc/letsencrypt/live/net.example.com/fullchain.pem \
    --tls-key /etc/letsencrypt/live/net.example.com/privkey.pem \
    -v info
```

Useful flags:

| argument          | description                                      |
|-------------------|--------------------------------------------------|
| `--port <port>`   | Signaling server port. Default is `8080`.        |
| `--tls`           | Serve HTTPS/WSS instead of plain HTTP/WebSocket. |
| `--tls-cert`      | TLS certificate chain file.                      |
| `--tls-key`       | TLS private key file.                            |
| `-v <level>`      | Log level: `error`, `warn`, `notice`, `info`, `debug`. |

With `--tls`, `peer-server` serves WSS on the same `--port`. Without `--tls`, it serves plain WS and should only be used
behind a reverse proxy that terminates TLS and forwards WebSocket traffic to it.

For a supervised deployment, run the same command under systemd or supervisor:

```ini
[program:webrtc-net]
command=/opt/webrtc-net/peer-server.bin.x86_64 --port 443 --tls --tls-cert /etc/letsencrypt/live/net.example.com/fullchain.pem --tls-key /etc/letsencrypt/live/net.example.com/privkey.pem -v info
autostart=true
autorestart=true
stderr_logfile=/var/log/webrtc-net.err.log
stdout_logfile=/var/log/webrtc-net.out.log
```

Open the signaling TCP port in your firewall. If you run the server on `443`, open `443/tcp`; if you run it on `8080`,
open `8080/tcp`.

### ICE servers

The signaling server does not provide STUN/TURN settings. js-dos passes ICE servers to WebRTC-NET from the player
configuration.

For LAN or simple public-network tests, a public STUN server may be enough:

```Javascript
iceServers: async () => [
    { urls: ["stun:stun.l.google.com:19302"] },
]
```

For production internet play, deploy a TURN server such as coturn and return both STUN and TURN entries:

```Javascript
iceServers: async () => [
    { urls: ["stun:turn.example.com:3478"] },
    {
        urls: [
            "turn:turn.example.com:3478?transport=udp",
            "turn:turn.example.com:3478?transport=tcp",
            "turns:turn.example.com:5349?transport=tcp",
        ],
        username: "user",
        credential: "password",
    },
]
```

If you use the coturn helper from WebRTC-NET, see `coturn-docker/` in the WebRTC-NET repository. It provides Docker
Compose setup for coturn, nginx, certbot, TLS certificates, and a browser latency test page.

For coturn, expose at least `3478/tcp`, `3478/udp`, `5349/tcp`, and the relay UDP range configured in coturn. The
WebRTC-NET helper uses `49152-65535/udp` for relay traffic.

### Configuring js-dos player

When the signaling server and ICE servers are ready, configure js-dos player:

```Javascript
const params = new URLSearchParams(location.search);

Dos(el, {
    url: "game.jsdos",
    startIpxServer: params.get("server") === "1",
    connectIpxAddress: params.get("connect") ?? undefined,
    net: {
        peerServer: "wss://net.example.com/ws",
        token: "my-game",
        secret: "fallback",
        iceServers: async () => [
            { urls: ["stun:turn.example.com:3478"] },
            {
                urls: ["turn:turn.example.com:3478?transport=udp"],
                username: "user",
                credential: "password",
            },
        ],
    },
});
```

`peerServer` is the WebSocket URL of your WebRTC-NET `peer-server`. Use `wss://` for HTTPS pages. `token` and `secret`
are passed to WebRTC-NET as the game token and game secret; current `peer-server` does not enforce user authentication,
but use stable values per deployment.

Use `startIpxServer` on the host player, then pass the host `peerId` or registered alias to another player through
`connectIpxAddress`. The actual game data will flow through WebRTC, using TURN only when direct peer-to-peer connection
cannot be established.

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
