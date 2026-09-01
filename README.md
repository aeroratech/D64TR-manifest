# D64TR Camera Manifest

This repository contains the `repo` manifest for the D64TR camera  project.

## Download the Source Code

1. Create the D64TR workspace:

   ```bash
   mkdir D64TR
   ```

2. Extract the **proprietary** components into the workspace:

   ```bash
   tar -xJf proprietary.tar.xz -C ./D64TR/
   ```

3. Enter the workspace, initialize, and synchronize the source tree:

   ```bash
   cd D64TR
   repo init -u https://github.com/aeroratech/D64TR-manifest.git -b main -m D64TR.xml
   repo sync -j "$(nproc)"
   ```

## Build Instructions

### Build Environment Requirements

- Operating system: Ubuntu 22.04
- Memory: at least 32 GB RAM
- Storage: at least 200 GB of available disk space

TODO
