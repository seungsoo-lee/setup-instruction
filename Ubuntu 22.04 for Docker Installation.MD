# Ubuntu 22.04 for Docker Installation

This gist contains steps to setup `Ubuntu 22.04` for docker installation.

----------------------------------------------------------------------------------------------------

## Update Ubuntu

```sh
$ sudo apt update
$ sudo apt full-upgrade --yes
$ sudo apt autoremove --yes
$ sudo apt autoclean --yes
$ reboot
```
----------------------------------------------------------------------------------------------------

## Install Development Tools

 - `$ sudo apt install build-essential pkg-config cmake cmake-qt-gui ninja-build valgrind`

----------------------------------------------------------------------------------------------------
