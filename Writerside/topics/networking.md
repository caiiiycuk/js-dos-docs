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
