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

## Installing Docker

```sh
$ sudo apt install apt-transport-https ca-certificates curl software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
$ sudo apt update
```
----------------------------------------------------------------------------------------------------
