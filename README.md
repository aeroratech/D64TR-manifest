# D64TR Camera Manifest

This repository contains the `repo` manifest for the D64TR camera  project.

## Download the Source Code

1. Create the D64TR workspace:

   ```bash
   mkdir D64TR
   ```

2. Extract the **d64tr_proprietary.tar.xz** components into the workspace:

   ```bash
   tar -xJf d64tr_proprietary.tar.xz -C ./D64TR/
   ```

3. Enter the workspace, initialize, and synchronize the source tree:

   ```bash
   cd D64TR
   repo init -u https://github.com/aeroratech/D64TR-manifest.git -b open -m D64TR.xml
   repo sync -j "$(nproc)"
   ```

## Build Instructions

### Build Environment Requirements

- Host operating system: Ubuntu 22.04 when using Docker, or Ubuntu 20.04 for a native build
- Memory: at least 32 GB RAM
- Storage: at least 200 GB of available disk space

The build host must be an x86_64 Linux machine. Choose one of the following
environment setup methods. Both methods use the Ubuntu 20.04 toolchain defined
by the project's [`docker/Dockerfile`](https://github.com/aeroratech/docker/blob/829adebf0565722c47d70a69314261304d22e5bd/Dockerfile).

### Option 1: Docker on Ubuntu 22.04

Build the image from the `docker` directory downloaded with the source tree.
Use that local directory as-is; it contains the project-specific Docker
configuration. Pass your host user and group IDs so files created in the
workspace remain owned by your host account:

```bash
cd D64TR
docker build \
  -f docker/Dockerfile \
  --build-arg userid="$(id -u)" \
  --build-arg groupid="$(id -g)" \
  --build-arg username="$(id -un)" \
  -t ubuntu:20.04-arm64 \
  docker
```

Start the build container with the source workspace mounted at `/workspace`:

```bash
docker run -it --rm \
  -v "$PWD:/workspace:rw" \
  -v /pkg:/pkg \
  -v /media:/media \
  -v /etc/localtime:/etc/localtime:ro \
  -v /etc/timezone:/etc/timezone:ro \
  -w /workspace \
  ubuntu:20.04-arm64
```

If the host provides Qualcomm tools under `/pkg`, keep that mount. The image
also creates the required `/lib/ld-linux-aarch64.so.1` symlink and configures
Bash as `/bin/sh`, so no additional container configuration is required.

### Option 2: Native Ubuntu 20.04

Install the host tools listed by the Dockerfile, then make `/bin/sh` point to
Bash as required by `setup-environment`:

```bash
sudo apt-get update
sudo apt-get install -y \
  apt-utils bc binutils binutils-arm-none-eabi binutils-dev bison \
  build-essential ca-certificates ccache checkinstall chrpath cmake curl cpio \
  debhelper diffstat flex fakechroot fakeroot gawk g++ g++-aarch64-linux-gnu \
  gcc gcc-aarch64-linux-gnu gcc-arm-none-eabi git gosu iputils-ping \
  kconfig-frontends kmod libarchive-dev libiberty-dev libncurses5 \
  libnewlib-arm-none-eabi libselinux1-dev libssl-dev libudev-dev \
  libwayland-bin libxml2-utils libxml-opml-simplegen-perl locales make meson \
  texinfo ninja-build openjdk-8-jdk openssl openssh-client openssh-server \
  pkg-config python3 python-is-python3 python3-pip python3-setuptools \
  python3-socks python3-wheel python3-yaml rsync sudo unzip udev usbutils \
  uuid-dev vim wget whiptail zlib1g-dev
sudo ln -sf /bin/bash /bin/sh
sudo ln -sf /usr/aarch64-linux-gnu/lib/ld-2.31.so /lib/ld-linux-aarch64.so.1
```

### Build the D64TR image

Run these commands in the Docker container or in the configured Ubuntu 20.04
host:

```bash
cd D64TR
MACHINE=qrb5165-rb5 DISTRO=qti-distro-ubuntu-fullstack-perf source setup-environment
bitbake qti-ubuntu-robotics-image
```

The generated image and deployment artifacts are placed under
`build-qti-distro-ubuntu-fullstack-perf/tmp-glibc/deploy/images/qrb5165-rb5/`.
