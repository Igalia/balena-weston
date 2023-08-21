# balena-weston

**Project Status: No Longer Under Active Development**

We regret to inform you that this project, which initially began as
an experiment to integrate WPE into the Balena Hub, has transitioned
into a state of discontinuation. While we appreciate the interest
and support that this project has garnered, due to various factors,
we are no longer actively maintaining or developing it.

As a result, the project is not being updated to incorporate the
latest versions or bugfixes. The current version available might not
reflect the most recent advancements or patches in the WPE ecosystem.

We understand that this might cause inconvenience to users who were
looking forward to continued enhancements. If you encounter issues or
are seeking updates, we recommend exploring alternative solutions or
resources that align with your requirements.

We appreciate your understanding and thank you for your interest in
this project. Should you have any further questions or inquiries,
please feel free to get in touch.

## Description

Weston is the reference implementation of a Wayland compositor, as well as an
useful environment in and of itself.

Out of the box, Weston provides a very basic desktop, or a full-featured
environment for non-desktop uses such as automotive, embedded, in-flight,
industrial, kiosks, set-top boxes and TVs. It also provides a library allowing
other projects to build their own full-featured environments on top of
Weston's core.

The core focus of Weston is correctness and reliability. Weston aims to be
lean and fast, but more importantly, to be predictable. Whilst Weston does
have known bugs and shortcomings, we avoid unknown or variable behaviour as
much as possible, including variable performance such as occasional spikes in
frame display time.

---

## Requirements

| Configuration      | Value
|--------------------|---------------
| Define DT overlays | `vc4-kms-v3d`


## Usage

### docker-compose file

``` yaml

version: '2'

volumes:
    weston:

services:
  weston:
    restart: always
    build: ./weston
    privileged: true
    volumes:
      - 'weston:/run/weston'

  myBlock:
    restart: always
    build: .
    privileged: true
    volumes:
      - 'weston:/run/weston'
```

## Customization

### Extend image configuration

*Dockerfile.template*

```Dockerfile
FROM igalia/balena-weston:%%BALENA_MACHINE_NAME%%

COPY data/	 /data/

COPY weston-init /weston-init
RUN chmod +x /weston-init

CMD [ "/weston-init" ]
```

*weston/weston-init*

```ini
...
[shell]
# Set a custom wallpaper
background-image=/data/wallpapers/MyWallpaper.jpg
...
```

### Environment variables

There are several environment variables that can be used to tweak
the Weston settings. The defaults should be okay for most cases.

| Environment variable                       | Options   | Default   | Description
|--------------------------------------------|-----------|-----------|---------------------
| **`WESTON_IDLE_TIME`**                     | `integer` | 0 (never) | Idle time in seconds (e.g. 900)
| **`WESTON_SHELL`**                         | `string`  | `desktop` | The shell to use (`desktop`, `fullscreen`, `kiosk` etc.)
| **`WESTON_OUTPUT_USE_CURRENT_MODE`**       | `0`, `1`  | `0`       | Inherit the display mode from KMS console
| **`WESTON_OUTPUT_MAX_WIDTH`**              | `integer` | `0`       | Maximum horizontal resolution the compositor may set
| **`WESTON_OUTPUT_MAX_HEIGHT`**             | `integer` | `0`       | Maximum vertical resolution the compositor may set

The maximum-resolution environment variables rely on the list of modes
advertised by the DRM subsystem. That means if you set for example the
maximum horizontal resolution to `1920` and the list contains `3840x2160`,
`2560x1440`, `1920x1080` and `1280x800`, `1920x1080` will be picked.

The shell used will be `${WESTON_SHELL}-shell.so`. You can use any shell
shipped with Weston by default.

## How to build

* Getting the sources

  ```bash
  git clone https://github.com/Igalia/balena-weston.git
  repo init -u https://github.com/Igalia/balena-weston.git -m manifest-hardknott.xml -b main
  repo sync --force-sync
  ```

* Generating the image root filesystem with Yocto:

  ```bash
  MACHINE=raspberrypi3
  ./build.sh
  ```

* Pushing the image to the registered Docker repository:

  ```bash
  MACHINE=raspberrypi3
  IMAGE_DOCKER_PATH=igalia
  # BALENA_BUILD_EXTRA_ARGS="-e"  # In case of balena is being executed on a different host architecture than the balena machine target
  ./push.sh
  ```

