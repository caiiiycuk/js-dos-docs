---
id: contributing-emulators
title: Contributing
---

To contribute to the `emulators` package, do the following:

1. Checkout `emulators` repository
  
  `git clone https://github.com/js-dos/emulators`

2. Install [emscripten sdk](https://emscripten.org/docs/tools_reference/emsdk.html), and confgure environment to use it.

3. Install [cmake](https://cmake.org/) and [gulp 4](https://gulpjs.com/).

4. Now you can build everything with `gulp` command

Native part of emulators is a plain cmake project, you can open it
in your favorite editor. The Project has the following targets:

1. **`sokol`** - js-dos v7 native version: dosbox + UI based on [sokol](https://github.com/floooh/sokol).
This version is exactly the same as the web version. **You should use this target
to contribute to js-dos v7.**
2. **`direct`** - target is used to build the web-direct version of js-dos v7.
You can compile it only with **emscripten**.
3. **`worker`** - target is used to build the web-worker version of js-dos v7.
You can compile it only with **emscripten**.
4. `dosbox` - original version of dosbox (UI based on SDL). You can use
it to compare behaviour between original dosbox and js-dos v7.
5. `libzip` - shared codes that contain implementation of zip.
6. `jsdos` - shared codes that contain implementation of dosbox.

## Protocol

The idea of new js-dos v7 is that all targets (native and web) have exactly
the same way to communicate between a client (native UI, browser UI) and dosbox.


```C
//
// Created by caiiiycuk on 27.02.2020.
//

#ifndef JS_DOS_JS_DOS_PROTOCOL_H
#define JS_DOS_JS_DOS_PROTOCOL_H

#include <keyboard.h>
#include <cstdint>

enum NetworkType {
  NETWORK_NA = -1,
  NETWORK_DOSBOX_IPX = 0,
};

// -- Client (Web Page)

void client_frame_set_size(int width, int height);
void client_frame_update_lines(uint32_t* lines, uint32_t count, void* rgba);
void client_sound_init(int freq);
void client_sound_push(const float* samples, int num_samples);
void client_stdout(const char* data, uint32_t amount);

void client_log(const char* tag, const char* message);
void client_warn(const char* tag, const char* message);
void client_error(const char* tag, const char* message);

void client_network_connected(NetworkType networkType, const char* address, uint32_t port);
void client_network_disconnected(NetworkType networkType);

#ifndef EMSCRIPTEN
extern void client_tick();
#endif

// -- Server (Worker)

extern int server_run();
extern void server_add_key(KBD_KEYS key, bool pressed, uint64_t pressedMs);
extern void server_mouse_moved(float x, float y, bool relative, uint64_t movedMs);
extern void server_mouse_button(int button, bool pressed, uint64_t pressedMs);
extern void server_mouse_sync(uint64_t syncMs);
extern void server_pause();
extern void server_resume();
extern void server_mute();
extern void server_unmute();
extern void server_exit();

extern void server_network_connect(NetworkType networkType, const char* address, uint32_t port);
extern void server_network_disconnect(NetworkType networkType);

#endif  // JS_DOS_JS_DOS_PROTOCOL_H
```

## Server

For simplicity, you can think that the server is a dosbox.
In the future, servers can be implemented with different emulators. Now we support
only **dosbox implementation** (look at `jsdos.cmake`).

### server_run()

Client should run this function when it's ready to start dosbox. This 
function will start the emulator. Client should prepare a file system for dosbox **it 
expects that `cwd` contains `.jsdos/dosbox.conf` file**. 

So you need to extract [js-dos bundle](jsdos-bundle.md) in some directory and start sokol binary
in this directory, and it will act exactly in the same way as direct/worker dosbox.


### server_add_key(keycode, pressed, timeMs)

This function adds keycode to the queue. They will be processed when dosbox poll keyboard
events.

### server_exit()

Terminates execution of dosbox and free resources.

## Client

Direct, worker, and sokol implementations share the same code for server part. But they are completely different,
because they implement UI and sound system for different platforms. In the original dosbox this was made
by SDL, it was hard-coupled with dosbox. js-dos clearly detach the emulator from its ui. You can easily add new
UI/sound system to dosbox. 

For example, let's look at sokol UI implementation. You can use it to debug and develop new features for js-dos.
Worker is a primary web implementation for js-dos v7. sokol implementation tries to work in a similar way: we start
dosbox emulator in main thread and client in new thread.

```C++
void runRuntime() {
  std::thread client(client_run);
  server_run();
  client.join();
}
```

### client_frame_set_size(width, height)

When the server starts, it will send the frame size of the dosbox window by invoking `client_frame_set_size`. You should allocate rgba buffer to store frame content. This function will be called each time when dosbox window size is changed.


```C++
extern int frameWidth = 0;
extern int frameHeight = 0;
extern uint32_t *frameRgba = 0;

void client_frame_set_size(int width, int height) {
  std::lock_guard<std::mutex> g(mutex);

  if (frameRgba) {
    delete[] frameRgba;
  }
  frameWidth = width;
  frameHeight = height;
  frameRgba = new uint32_t[width * height];
}
```

### client_frame_update_lines(lines, count, rgba)

This method will be called each time if contents of dosbox window are changed. dosbox implementation 
will send only changed lines. You need to update your frame buffer correctly.

Dirty region format (lines argument):
* line number [0, height)
* count of changed lines
* offset in passed buffer (rgba argument)

```C++
void client_frame_update_lines(uint32_t *lines, uint32_t count, void *rgba) {
  std::lock_guard<std::mutex> g(mutex);

  frameCount++;
  if (!frameRgba) {
    return;
  }

  for (uint32_t i = 0; i < count; ++i) {
    uint32_t start = lines[i * 3];
    uint32_t count = lines[i * 3 + 1];
    uint32_t offset = lines[i * 3 + 2];
    memcpy(&frameRgba[start * frameWidth], (char *)rgba + offset,
           sizeof(uint32_t) * count * frameWidth);
  }
}
```

Implementing `client_frame_set_size` and `client_frame_update_lines` is enough to render
dosbox window:

```C++
// ... 
   appDescription.frame_cb = []() { sokolFrame(); };
// ...

void sokolFrame() {
  std::lock_guard<std::mutex> g(mutex);

  // ...
  
  sg_image_content imageContent = {};
  imageContent.subimage[0][0] = {};
  imageContent.subimage[0][0].ptr = frameRgba;
  imageContent.subimage[0][0].size =
      (state->width) * (state->height) * (int)sizeof(uint32_t);

  sg_update_image(state->bind.fs_images[0], &imageContent);

  sg_begin_default_pass(&state->pass, state->width, state->height);
  sg_apply_pipeline(state->pipeline);
  sg_apply_bindings(&state->bind);
  sg_draw(0, 4, 1);
  sg_end_pass();
  sg_commit();

  renderedFrame = frameCount;
}
```

### client_sound_init(freq);

Called when the dosbox needs to initialize the sound system. 

```C++
void client_sound_init(int freq) {
  saudio_desc audioDescription = {};
  audioDescription.sample_rate = static_cast<int>(freq);
  audioDescription.num_channels = 1;

  saudio_setup(&audioDescription);
  assert(saudio_isvalid());
}
```

### client_sound_push(samples, num_samples)

This method is called each time when new sound samples should be pushed to audio device.
With sokol implementation is very simple:

```C++
void client_sound_push(const float *samples, int num_samples) {
  saudio_push(samples, num_samples);
}
```

### client_stdout(data, amount)

This method will be called each time when dosbox prints something to its console.

### Communicate to server

Each time when key is pressed we should send event to dosbox:

```C++
// ...
  appDescription.event_cb = [](const sapp_event *event) {
    switch (event->type) {
      case SAPP_EVENTTYPE_KEY_DOWN:
      case SAPP_EVENTTYPE_KEY_UP:
        keyEvent(event);
        break;
      default:;
    }
  };
// ...


void keyEvent(const sapp_event *event) {
  server_add_key(
      (KBD_KEYS)event->key_code,
      event->type == SAPP_EVENTTYPE_KEY_DOWN,
      GetMsPassedFromStart());
}
```

When user closes sokol window we need to stop server:
```C++
  appDescription.cleanup_cb = []() { server_exit(); };
```


That is. Check complete [source](https://github.com/js-dos/emulators/tree/main/src/dos/sokol) of sokol implementation.

## Testing

If the `gulp` command is finished successfully then you can run emulators tests.
To do this, run a static web server to host the `dist` directory. For example, with `http-server`:

```
http-server dist
```

and open test page in browser:

```
firefox http://127.0.0.1:8080/test/test.html
```

all tests should pass.

## Running native js-dos v7

As said above, you need to compile a `sokol` target with your favorite C++ toolkit. It will generate `sokol`
executable. Next, you need to download some [js-dos bundle](jsdos-bundle.md) for example
[digger](https://cdn.dos.zone/original/2X/2/24b00b14f118580763440ecaddcc948f8cb94f14.jsdos).

`js-dos bundle` is a plain zip archive, you need to extract it in some folder. After that you
should run `sokol` executable from that folder (cwd must be the root of the extracted bundle). 

## Using Docker

You can use docker image to develop emulators core. The image has already configured
everything to build emulators core and start emulators tests.

### Build image

```
    docker build -t emulators . 
```

### Test image


```
    docker run -p 8080:8080 -ti emulators
```

Open `http://localhost:8080` in browser, all tests should pass


### Development

Run inside the project directory:

```
    docker run -v `pwd`/src:/app/src -v `pwd`/test:/app/test -v `pwd`/dist:/app/dist -ti emulators bash
    source /emsdk/emsdk_env.sh
    gulp OR ./node_modules/.bin/tsc --watch
```

Use your code editor to edit the content of src and test. 
In the docker VM you can run `gulp` to build everything into `dist` OR use
`./node_modules/.bin/tsc --watch` if you need only compile time checks.


## Contributing on github

To contribute your code please create PR on github, and check if all tests passed.
