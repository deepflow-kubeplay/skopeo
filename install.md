# Installing Skopeo

## Distribution Packages
`skopeo` may already be packaged in your distribution.

### Fedora

```sh
sudo dnf -y install skopeo
```

### RHEL/CentOS ≥ 8 and CentOS Stream

```sh
sudo dnf -y install skopeo
```

### RHEL/CentOS ≤ 7.x

```sh
sudo yum -y install skopeo
```

### openSUSE

```sh
sudo zypper install skopeo
```

### Alpine

```sh
sudo apk add skopeo
```

### macOS

```sh
brew install skopeo
```

### Nix / NixOS
```sh
$ nix-env -i skopeo
```

### Debian

The skopeo package is available on [Bullseye](https://packages.debian.org/bullseye/skopeo),
and Debian Testing and Unstable.

```bash
# Debian Bullseye, Testing or Unstable/Sid
sudo apt-get update
sudo apt-get -y install skopeo
```

### Raspberry Pi OS arm64 (beta)

Raspberry Pi OS uses the standard Debian's repositories,
so it is fully compatible with Debian's arm64 repository.
You can simply follow the [steps for Debian](#debian) to install Skopeo.


### Ubuntu

The skopeo package is available in the official repositories for Ubuntu 20.10
and newer.

```bash
# Ubuntu 20.10 and newer
sudo apt-get -y update
sudo apt-get -y install skopeo
```

The [Kubic project](https://build.opensuse.org/package/show/devel:kubic:libcontainers:stable/skopeo)
provides packages for Ubuntu 20.04 (it should also work with direct derivatives like Pop!\_OS).

```bash
. /etc/os-release
echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${VERSION_ID}/ /" | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_${VERSION_ID}/Release.key | sudo apt-key add -
sudo apt-get update
sudo apt-get -y upgrade
sudo apt-get -y install skopeo
```

### Windows
Skopeo has not yet been packaged for Windows. There is an [open feature
request](https://github.com/containers/skopeo/issues/715) and contributions are
always welcome.


## Container Images

Skopeo container images are available at `quay.io/skopeo/stable:latest`.
For example,

```bash
podman run docker://quay.io/skopeo/stable:latest copy --help
```

[Read more](./contrib/skopeoimage/README.md).


## Building from Source

Otherwise, read on for building and installing it from source:

To build the `skopeo` binary you need at least Go 1.12.

There are two ways to build skopeo: in a container, or locally without a
container. Choose the one which better matches your needs and environment.

### Building without a container

Building without a container requires a bit more manual work and setup in your
environment, but it is more flexible:

- It should work in more environments (e.g. for native macOS builds)
- It does not require root privileges (after dependencies are installed)
- It is faster, therefore more convenient for developing `skopeo`.

Install the necessary dependencies:

```bash
# Fedora:
sudo dnf install gpgme-devel libassuan-devel btrfs-progs-devel device-mapper-devel
```

```bash
# Ubuntu (`libbtrfs-dev` requires Ubuntu 18.10 and above):
sudo apt install libgpgme-dev libassuan-dev libbtrfs-dev libdevmapper-dev pkg-config
```

```bash
# macOS:
brew install gpgme
```

```bash
# openSUSE:
sudo zypper install libgpgme-devel device-mapper-devel libbtrfs-devel glib2-devel
```

Make sure to clone this repository in your `GOPATH` - otherwise compilation fails.

```bash
git clone https://github.com/containers/skopeo $GOPATH/src/github.com/containers/skopeo
cd $GOPATH/src/github.com/containers/skopeo && make bin/skopeo
```

By default the `make` command (make all) will build bin/skopeo and the documentation locally.

Building of documentation requires `go-md2man`. On systems that do not have this tool, the
document generation can be skipped by passing `DISABLE_DOCS=1`:
```
DISABLE_DOCS=1 make
```

### Building documentation

To build the manual you will need go-md2man.

```bash
# Debian:
sudo apt-get install go-md2man
```

```
# Fedora:
sudo dnf install go-md2man
```

```
# MacOS:
brew install go-md2man
```

Then

```bash
make docs
```

### Building in a container

Building in a container is simpler, but more restrictive:

- It requires the `podman` command and the ability to run Linux containers.
- The created executable is a Linux executable, and depends on dynamic libraries
  which may only be available only in a container of a similar Linux
  distribution.

```bash
$ make binary
```

### Installation

Finally, after the binary and documentation is built:

```bash
sudo make install
```

### Building a static binary

There have been efforts in the past to produce and maintain static builds, but the maintainers prefer to run Skopeo using distro packages or within containers. This is because static builds of Skopeo tend to be unreliable and functionally restricted. Specifically:
- Some features of Skopeo depend on non-Go libraries like `libgpgme` and `libdevmapper`.
- Generating static Go binaries uses native Go libraries, which don't support e.g. `.local` or LDAP-based name resolution.

That being said, if you would like to build Skopeo statically, you might be able to do it by combining all the following steps.
- Export environment variable `CGO_ENABLED=0` (disabling CGO causes Go to prefer native libraries when possible, instead of dynamically linking against system libraries).
- Set the `BUILDTAGS=containers_image_openpgp` Make variable (this remove the dependency on `libgpgme` and its companion libraries).
- Clear the `GO_DYN_FLAGS` Make variable (which otherwise seems to force the creation of a dynamic executable).

The following command implements these steps to produce a static binary in the `bin` subdirectory of the repository:

```bash
docker run -v $PWD:/src -w /src -e CGO_ENABLED=0 golang \
make BUILDTAGS=containers_image_openpgp GO_DYN_FLAGS=
```

Keep in mind that the resulting binary is unsupported and might crash randomly. Only use if you know what you're doing!

For more information, history, and context about static builds, check the following issues:

- [#391] - Consider distributing statically built binaries as part of release
- [#669] - Static build fails with segmentation violation
- [#670] - Fixing static binary build using container
- [#755] - Remove static and in-container targets from Makefile
- [#932] - Add nix derivation for static builds
- [#1336] - Unable to run skopeo on Fedora 30 (due to dyn lib dependency)
- [#1478] - Publish binary releases to GitHub (request+discussion)

[#391]: https://github.com/containers/skopeo/issues/391
[#669]: https://github.com/containers/skopeo/issues/669
[#670]: https://github.com/containers/skopeo/issues/670
[#755]: https://github.com/containers/skopeo/issues/755
[#932]: https://github.com/containers/skopeo/issues/932
[#1336]: https://github.com/containers/skopeo/issues/1336
[#1478]: https://github.com/containers/skopeo/issues/1478
