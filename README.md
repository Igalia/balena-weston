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

| Environment variable   | Default               | Example
|------------------------|-----------------------|---------------------
| **`WESTON_IDLE_TIME`** | 0 (never)             | 900


## How to build

* Getting the sources

  ```bash
  git clone https://gitlab.com/browsers/docker-balena-weston.git
  repo init -u https://gitlab.com/browsers/docker-balena-weston.git -m manifest-hardknott.xml -b main
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

