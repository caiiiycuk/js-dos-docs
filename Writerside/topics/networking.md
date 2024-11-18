# Networking

Some DOS games offer multiplayer gaming based on ipx, modem, or direct connection.
Using a browser isn't possible to directly connect two clients. BUT, using js-dos cloud, 
you can emulate direct connection.

> Only IPX protocol is supported right now.

Here is tutorial video how to use networking with js-dos:

<video src="https://www.youtube.com/watch?v=YH22lZ1EUjM"/>

By default, users able to enjoy these games using the Netherlands server, which is provided by js-dos. 

> You need to enable networking features in player using [options](Player-API.md#options)

## Deploy own ipx backend

As said js-dos provide Netherlands server for free, but it works well only for Europe region. To improve player 
experience you can deploy own instance of ipx backend.

> If your deployment is permanent and well maintained, then please let us know, we will add it to
> js-dos player defaults
> 
{style="note"}

### The Server

The server code is licensed under GPL2 and you can find it [here](https://github.com/caiiiycuk/dosbox-ipx-server).
This server works as relay it connects multiple js-dos clients together. You need to deploy it on some public available
server.

1. Download the server [binary](https://github.com/caiiiycuk/dosbox-ipx-server/releases/)
2. Run it as follows:

   ```
   ./server -c cert.pem -k privkey.pem
   ```
   
   | Argument        | Description              |
   |-----------------|--------------------------|
   | ./server        | server binary            |
   | -c cert.pem     | path to SSL certificate  |
   | -k privekey.pem | path to private key file |

### Configuring js-dos player

When server is started you can use it with js-dos player. 
For example, if your server is available on host `myipx.com`, [js-dos configuration](Player-API.md) will be:

```javascript
    const params = new URLSearchParams(location.search);
// ...
    Dos(el, {
          server: params.get("server"),
          room: params.get("room"),
          ipx: [{
              name: "myipx",
              host: "wss://myipx.com",
            }, {
              name: "dos.zone",
              host: "wss://netherlands.dos.zone",
            }
          ],
    });
```

The first element in ipx array will be used as default ipx server.