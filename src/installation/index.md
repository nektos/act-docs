# Installation

## Necessary prerequisites for running `act`

`act` depends on `docker` (exactly Docker Engine API) to run workflows.

If you are using macOS, please be sure to follow the steps outlined in [Docker Docs for how to install Docker Desktop for Mac](https://docs.docker.com/docker-for-mac/install/).

If you are using Windows, please follow steps for [installing Docker Desktop on Windows](https://docs.docker.com/docker-for-windows/install/).

If you are using Linux, you will need to [install Docker Engine](https://docs.docker.com/engine/install/).

To use `act` with remote Docker Engine API compatible host, see [Custom container engine](./../usage/custom_engine.md)
`act` is currently not supported with `podman` or other container backends (it might work, but it's not guaranteed).
Please see [#303](https://github.com/nektos/act/issues/303) for updates.

## Pre-built artifacts

### Bash script

You can easily install a pre-built `act` executable on any system with `bash` via below commandline

```shell
curl --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/nektos/act/master/install.sh | sudo bash
```

⚠ *Script can't install non-released versions* ⚠

### Manual download of prebuilt executable

Prebuilt executables for multiple platforms are available via [latest release](https://github.com/nektos/act/releases/latest) page.
You can unpack and run them in terminal specifying full path or add them to one of the paths included in `PATH` environment variable.

- [macOS 64-bit (Apple Silicon)](https://github.com/nektos/act/releases/latest/download/act_Darwin_arm64.tar.gz)
- [macOS 64-bit (Intel)](https://github.com/nektos/act/releases/latest/download/act_Darwin_x86_64.tar.gz)
- [Linux 64-bit (arm64/aarch64)](https://github.com/nektos/act/releases/latest/download/act_Linux_arm64.tar.gz)
- [Linux 32-bit (armv6)](https://github.com/nektos/act/releases/latest/download/act_Linux_armv6.tar.gz)
- [Linux 32-bit (armv7)](https://github.com/nektos/act/releases/latest/download/act_Linux_armv7.tar.gz)
- [Linux 32-bit (i386/x86)](https://github.com/nektos/act/releases/latest/download/act_Linux_i386.tar.gz)
- [Linux 64-bit (amd64/x86_64)](https://github.com/nektos/act/releases/latest/download/act_Linux_x86_64.tar.gz)
- [Windows 64-bit (arm64/aarch64)](https://github.com/nektos/act/releases/latest/download/act_Windows_arm64.zip)
- [Windows 32-bit (armv7)](https://github.com/nektos/act/releases/latest/download/act_Windows_armv7.zip)
- [Windows 32-bit (armv7)](https://github.com/nektos/act/releases/latest/download/act_Windows_armv7.zip)
- [Windows 32-bit (i386/x86)](https://github.com/nektos/act/releases/latest/download/act_Windows_i386.zip)
- [Windows 64-bit (amd64/x86_64)](https://github.com/nektos/act/releases/latest/download/act_Windows_x86_64.zip)

### Build from source

Requires Go toolchain 1.18+

```shell
git clone https://github.com/nektos/act.git
cd act/
make build
# OR
go build -ldflags "-X main.version=$(git describe --tags --dirty --always | sed -e 's/^v//')" -o dist/local/act main.go
```

## Installation via software package manager

`act` is available in below package repositories

- [AUR](./aur.md) (`Linux`)
- [Homebrew](./brew.md) (`Linux`, `macOS`)
- [Chocolatey](./chocolatey.md) (`Windows`)
- [COPR](./copr.md) (`Linux`)
- [GitHub CLI](./gh.md) (`Linux`, `macOS`, `Windows`)
- [Nix/NixOS](./nix.md) (`Linux`, `macOS`)
- [MacPorts](./ports.md) (`macOS`)
- [Scoop](./scoop.md) (`Windows`)
- [Winget](./winget.md) (`Windows`)

### Packaging status

[![Packaging status](https://repology.org/badge/vertical-allrepos/act-run-github-actions.svg)](https://repology.org/project/act-run-github-actions/versions)
