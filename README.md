# balena-weston

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

| Environment variable                 | Default               | Example
|--------------------------------------|-----------------------|---------------------
| **`WESTON_IDLE_TIME`**               | 0 (never)             | 900

#### Compositor settings

The browser relies on the Weston compositor to work. While the defaults are
fine for most uses, some tweaks are allowed regarding the output resolution.

| Environment variable                       | Options   | Default | Description
|--------------------------------------------|-----------|---------|---------------------------------------------------
| **`WESTON_OUTPUT_USE_CURRENT_MODE`**       | `0`, `1`  | `0`     | Inherit the display mode from KMS console
| **`WESTON_OUTPUT_MAX_WIDTH`**              | `integer` | `0`     | Maximum horizontal resolution the compositor may set
| **`WESTON_OUTPUT_MAX_HEIGHT`**             | `integer` | `0`     | Maximum vertical resolution the compositor may set

The maximum-resolution environment variables rely on the list of modes
advertised by the DRM subsystem. That means if you set for example the
maximum horizontal resolution to `1920` and the list contains `3840x2160`,
`2560x1440`, `1920x1080` and `1280x800`, `1920x1080` will be picked.

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
  ./push.sh
  ```

