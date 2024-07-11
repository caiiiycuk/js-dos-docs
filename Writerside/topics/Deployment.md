# Deployment

Currently, sockdrive reporistory has only one active deployment, is `sockdrive.js-dos.com:8001`.
Geographically it is located in Europe, because of that speed can vary between regions.

We are looking forward to maintainers from other regions. If you want to create a sockdrive mirror, 
please let me know.

## New deployment

You are also able to create your own sockdrive server with own drives, and run it locally or globally. 

sockdrive server should work fine on any linux distribution like Ubuntu. Download the [**server**](https://cdn.dos.zone/custom/tools/sockdrive-backend-11.07.2024.zip) and
unpack it somewhere.

Folder structure:

```
- backend         // sockdrive server executable
- cli             // sockdrive cli interface
- config/         // configuration directory
  - props.json    // configuration file
  - drives/       // drive storage  
  - templates/    // drive templates
```

included templates:
* **fat16-256m** — empty 256Mb drive formatted in fat16
* **fat32-2gb** — empty 2Gb drive formatted in fat32

included drives (`./cli list system`):

| owner  | name       | description                                 |
|--------|------------|---------------------------------------------|
| system | fat16-256m | Blank drive based on corresponding template |
| system | fat32-2gb  | Blank drive based on corresponding template |
| system | test       | Test drive used in backend unit tests       |
| system | dos7.1-v1  | DOS 7.1 with CDROM support                  |
| system | win95-v1   | Minimal (256Mb) Windows 95 installation     |
| system | win95-v2   | Windows 95 (2Gb) with daemon tools          |
| system | win98-v1   | Windows 98 (2Gb) with daemon tools          |                             

configuration file properties (`config/props.json`):

| property  | description                                                                                             | default value                 |
|-----------|---------------------------------------------------------------------------------------------------------|-------------------------------|
| cert      | path to ssl certificate file for wss:// support                                                         | js-dos.com certificate        |
| key       | path to ssl certificate key file for wss:// support                                                     | js-dos.com certificate        |
| port      | server port to use                                                                                      | 8001                          |
| templates | path to folder with templates                                                                           | ./config/templates            |
| drivers   | path to folder with drives                                                                              | ./config/drives               |
| premium   | list of emails that have full access to sockdrive even if this emails does not have js-dos subscription | dos.zone, caiiiycuk@gmail.com |

> you can leave cert and key empty string, then server will work in ws:// mode
> 

To start the server run backend executable:

```
./backend
```

At the beginning server will print configuration used and information about port used, like this:
```
2024-06-28T03:26:21.456339Z  INFO backend: started non-secured server on 0.0.0.0:8001
```

Now you can use it with js-dos player, just add your server as sockdrive endpoint:

```Javascript
Dos(document.getElementById("dos"), {
    sockdriveBackend: {
        name: "custom",
        host: "localhost:8001",
    },
});
```

> Users that have js-dos subscription will be able to write and create new drives on your server, moreover, users
> that listed in config will also be able to write and create drives even without subscription.
> Be careful sockdrive server can consume a lot of disk space.
>
{style="warning"}

### Open file limits

You should increase the limit of open files, edit `/etc/security/limits.conf` add the following lines:

```
* soft nofile 1000000
* hard nofile 1000000
```

And restart PC.


### Cli commands

| Command                                          | Description                                             |
|--------------------------------------------------|---------------------------------------------------------|
| **create** (owner) (name) (template)             | Creates new drive owner/name from template              |
| `./cli create me empty fat16-256m`               | Will create me/empty drive based on fat16-256m template |   
|                                                  |                                                         |
| **fork** (owner) (name) (fork_owner) (fork_name) | Create new drive from existent one                      |
| `./cli fork system win95-v1 me mywin`            | Will create me/mywin from system/win95-v1               |
|                                                  |                                                         |
| **list** (owner)                                 | List all drives of owner + system drives                |
| `./cli list me`                                  | Will list all drives of me                              |
|                                                  |                                                         |
| **delete** (owner) (name)                        | Delete specified drive                                  |
| `./cli delete me mywin`                          | Will delete drive my/mywin                              |

>  be careful with delete command **dependent drives will stop working** correctly
> 
{style="warning"}