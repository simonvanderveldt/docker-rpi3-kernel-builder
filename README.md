# Docker Raspberry Pi 3 kernel image builder [![Docker Build Status](https://img.shields.io/docker/build/simonvanderveldt/rpi3-kernel-builder.svg)](https://hub.docker.com/r/simonvanderveldt/rpi3-kernel-builder/)
Docker image to build a Raspberry Pi 3 kernel image for the `arm` architecture

## How to use
Building a kernel using the rpi3-kernel-builder Docker image works according to the following conventions.
Configuration is done using environment variables:
- The kernel sources should be made available at `/workdir`
- The build will output to `/workdir/build`
- To apply patches to the kernel sources make the patches available at `PATCH_DIRS`. This is a space separated array of paths.
The patches need to have the extension `.patch`
- The kernel can be configured using a `defconfig` or using `all*config`. The default is to configure using `bcm2709_defconfig`
  - The override the default `defconfig` set `DEFCONFIG` to the `defconfig`'s name and make sure the `defconfig` is made available at the correct path.
  For example `DEFCONFIG=bcm2709_defconfig` with `bcm2709_defconfig` being available at `arch/arm/configs/bcm2709_defconfig`'
  - To configure using `all*config` set `ALLCONFIG` to one of the allconfig options `allyesconfig`, `allmodconfig`, `allnoconfig` or `randconfig`.
  To override which config is used as the starting point set [`KCONFIG_ALLCONFIG`](https://github.com/raspberrypi/linux/blob/560909d433109e3da08757237f30576c71697914/Documentation/kbuild/kconfig.txt#L51) to the path of the config file. For example `ALLCONFIG=/workdir/configs/myminimalconfig`

### Example usage
```
# Checkout kernel sources
$ git clone --single-branch --branch rpi-4.9.y --depth 1 https://www.github.com/raspberrypi/linux

# Run the docker image with volume mounts for the linux sources and the build directory
$ cd linux
$ docker run --rm -ti -v "${PWD}":/workdir rpi3-kernel-builder

# And when the build is done
$ ls -ahl build/
total 28M
drwxr-xr-x 2 simon simon    6 Feb  3 23:21 .
drwxr-xr-x 5 simon simon    6 Feb  3 23:08 ..
-rw-r--r-- 1 root  root  178K Feb  3 23:21 linux-firmware-image-4.9.59-v7_4.9.59-v7-1_armhf.deb
-rw-r--r-- 1 root  root   11M Feb  3 23:21 linux-headers-4.9.59-v7_4.9.59-v7-1_armhf.deb
-rw-r--r-- 1 root  root   16M Feb  3 23:21 linux-image-4.9.59-v7_4.9.59-v7-1_armhf.deb
-rw-r--r-- 1 root  root  830K Feb  3 23:21 linux-libc-dev_4.9.59-v7-1_armhf.deb
```

## Attribution
Based on:
- https://github.com/DieterReuter/rpi64-kernel
- https://autostatic.com/2017/06/27/rpi-3-and-the-real-time-kernel/
