# pspdev

[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/pspdev/pspdev/CI?label=CI&logo=github&style=for-the-badge)](https://github.com/pspdev/pspdev/actions?query=workflow%3ACI)[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/pspdev/pspdev/CI-Docker?label=CI-Docker&logo=github&style=for-the-badge)](https://github.com/pspdev/pspdev/actions?query=workflow%3ACI-Docker)[![Docker Pulls](https://img.shields.io/docker/pulls/pspdev/pspdev?style=for-the-badge)](https://hub.docker.com/r/pspdev/pspdev/tags)

Main PSP Repo for building the whole `PSP Development` environment in your local machine.

This program will automatically build and install the whole compiler and other tools used in the creation of homebrew software for the Sony PlayStation Portable® videogame system.

## What these scripts do

These scripts download (`git clone`) and install:

-   [psptoolchain](https://github.com/pspdev/psptoolchain "psptoolchain")
-   [pspsdk](https://github.com/pspdev/pspsdk "pspsdk")
-   [psp-packages](https://github.com/pspdev/psp-packages "psp-packages")
-   [psplinkusb](https://github.com/pspdev/psplinkusb "psplinkusb")
-   [ebootsigner](https://github.com/pspdev/ebootsigner "ebootsigner")

## Requirements

1.  Install gcc/clang, make, cmake, patch, git, texinfo, flex, bison, gettext, wget, gsl, gmp, mpfr, mpc, libusb, readline, libarchive, gpgme, bash, openssl and libtool if you don't have those.
We offer a script to help you for installing dependencies:

### Ubuntu/Debian
```bash
sudo ./prepare-debian-ubuntu.sh
```

### Fedora
```bash
sudo ./prepare-fedora.sh
```

### OSX
```bash
sudo ./prepare-mac-os.sh
```

2.  _Optional._ If you are upgrading from the previous version of the PSPDEV environment, it is highly recommended to remove the content of the PSPDEV folder before upgrade. This is a necessary step after the major toolchain upgrade.
```bash
sudo rm -rf $PSPDEV
```

3.  Ensure that you have enough permissions for managing PSPDEV location (default to `/usr/local/pspdev`, but you can use a different path). PSPDEV location MUST NOT have spaces or special characters in its path! PSPDEV should be an absolute path. On Unix systems, if the command `mkdir -p $PSPDEV` fails for you, you can set access for the current user by running commands:
```bash
export PSPDEV=/usr/local/pspdev
sudo mkdir -p $PSPDEV
sudo chown -R $USER: $PSPDEV
```

4.  Add this to your login script (example: `~/.bash_profile`)
    **Note:** Ensure that you have full access to the PSPDEV path. You can change the PSPDEV path with the following requirements: only use absolute paths, don't use spaces, only use Latin characters.
```bash
export PSPDEV=/usr/local/pspdev
export PATH=$PATH:$PSPDEV/bin
```

5.  Run build-all.sh
```bash
./build-all.sh
```

## Docker generation

This repo also uses CI/CD to create a docker image called `pspdev/pspdev:latest` per change. This is useful if you're a developer that wants to create/port an application to the PSP. You can compile your project using this docker image.

## Extra steps

If you want, you can _JUST_ install the extra dependencies as `psplinkusb and ebootsigner`. To achieve this execute
```bash
./build-extra.sh
```

### macOS

If you download the pre-built macOS binaries and get a security error such as _`"pspsh" cannot be opened because the developer cannot be verified.`_, you can remove the quarantine attribute by running:
```bash
xattr -dr com.apple.quarantine path/to/prebuilt/pspdev
```

### Local package builds

The toolchain (binutils, gcc), the SDK (pspsdk) and the host tools are built locally. However the provided packages (psp-packages) are installed via `psp-pacman` (or a similar mechanism if not available), which fetches packages from [github releases](https://github.com/pspdev/psp-packages/releases). If you wish to build these packages locally, you might define the variable *LOCAL_PACKAGE_BUILD* which will force pacman to build the packages from source instead of downloading them:

```bash
LOCAL_PACKAGE_BUILD=1 ./build-all.sh
```

This is particularly useful if you are testing changes in the toolchain (ie. gcc or binutils) and want to test your changes end to end. It can also be useful if you want a hermetic build and don't want to use any of the provided binaries.

## Thanks


## Foreign architecture builds with docker buildx

### aarch64 Ubuntu example

- Install qemu-user-static and binfmt-misc
- Install this https://github.com/docker/buildx

```sh
mkdir $HOME/psptmp
docker buildx create --use
docker buildx build --platform linux,arm64 -o type=local,dest=$HOME/psptmp  -f cross/Dockerfile.cross-ubuntu .
```

After these commands finish, you should have the typical /usr/local/pspdev cross-compiled for aarch64 on ubuntu 22.04 at `$HOME/psptmp/linux_arm64/usr/local/pspdev`. Copy this out to your aarch64 ubuntu machine (probably tar it first) and delete the rest of the junk in $HOME/psptmp
