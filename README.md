## How to build

* Getting the sources

  ```bash
  git clone https://gitlab.com/browsers/balena-weston.git
  repo init -u https://gitlab.com/browsers/balena-weston.git -m manifest-hardknott.xml -b main
  repo sync --force-sync
  ```

* Generating the image root filesystem with Yocto:

  ```bash
  MACHINE=raspberrypi3
  ./build.sh
  ```

* Importing the image as Docker image:

  ```bash
  MACHINE=raspberrypi3
  IMAGE_DOCKER_PATH=igalia
  ./import.sh
  docker images
  ```

* Pushing the image to the registered Docker repository:

  ```bash
  docker push igalia/balena-weston:raspberrypi3
  ```

