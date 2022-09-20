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

## Install Docker

```sh
$ sudo apt install apt-transport-https ca-certificates curl software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
$ sudo apt update
$ apt-cache policy docker-ce
$ sudo apt install docker-ce
```
----------------------------------------------------------------------------------------------------

## Configure daemon.json

```sh
cat <<EOF | sudo tee /etc/docker/daemon.json
{
    "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "100m"
    },
    "storage-driver": "overlay2"
}
EOF
$ sudo systemctl restart docker
$ sleep 1
```
----------------------------------------------------------------------------------------------------

## Execute the Docker Command without sudo (optional)

```sh
$ sudo usermod -aG docker ${USER}
$ su - ${USER}
$ groups
```
----------------------------------------------------------------------------------------------------

## Bypass to run docker command

```sh
$ sudo chmod 666 /var/run/docker.sock # bypass to run docker command
```
----------------------------------------------------------------------------------------------------

## Install docker-compose

```sh
$ sudo curl -sL https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```
----------------------------------------------------------------------------------------------------

