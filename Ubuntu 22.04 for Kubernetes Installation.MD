# Ubuntu 22.04 for Kubernetes Installation

This gist contains steps to setup `Ubuntu 22.04` for kubernetes installation.

----------------------------------------------------------------------------------------------------
## Disable the swap partition

```sh
$ sudo vi /etc/fstab
(comment out the line for swap)
$ sudo reboot
```
----------------------------------------------------------------------------------------------------
## Install Kubernetes
```sh
# update repo
$ sudo apt-get update

# install apt-transport-https
$ sudo apt-get install -y apt-transport-https ca-certificates curl

# add the key for kubernetes repo
$ sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

# add sources.list.d
$ echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

# update repo
$ sudo apt-get update

# install kubernetes (Docker-based)
$ sudo apt-get install -y kubeadm=1.23.0-00 kubelet=1.23.0-00 kubectl=1.23.0-00

# exclude kubernetes packages from updates
$ sudo apt-mark hold kubeadm kubelet kubectl

# mount bpffs (for Cilium)
$ echo "bpffs                                     /sys/fs/bpf     bpf     defaults          0       0" | sudo tee -a /etc/fstab

# enable ip forwarding
if [ $(cat /proc/sys/net/ipv4/ip_forward) == 0 ]; then
    sudo bash -c "echo '1' > /proc/sys/net/ipv4/ip_forward"
    sudo bash -c "echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf"
fi

# disable rp_filter
$ sudo bash -c "echo 'net.ipv4.conf.all.rp_filter = 0' > /etc/sysctl.d/99-override_cilium_rp_filter.conf"
$ sudo systemctl restart systemd-sysctl
```
----------------------------------------------------------------------------------------------------
## Initialize Kubernetes (Master Node)
```sh
# set default
if [ "$CNI" == "" ]; then
    CNI=cilium
fi

# use docker as default CRI
if [ "$CRI_SOCKET" == "" ]; then
    # if docker, let kubeadm figure it out
    if [ -S /var/run/docker.sock ]; then
        CRI_SOCKET=""
    elif [ -S /var/run/containerd/containerd.sock ]; then
        CRI_SOCKET=unix:///var/run/containerd/containerd.sock
    elif [ -S /var/run/crio/crio.sock ]; then
        CRI_SOCKET=unix:///var/run/crio/crio.sock
    fi
fi

# reload env
. ~/.bashrc

# turn off swap
sudo swapoff -a

# activate br_netfilter
sudo modprobe br_netfilter
sudo bash -c "echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables"
sudo bash -c "echo 'net.bridge.bridge-nf-call-iptables=1' >> /etc/sysctl.conf"

# initialize the master node
sudo kubeadm init --cri-socket=$CRI_SOCKET --pod-network-cidr=10.244.0.0/16 | tee -a ~/k8s_init.log

# make kubectl work for non-root user
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $USER:$USER $HOME/.kube/config
export KUBECONFIG=$HOME/.kube/config
echo "export KUBECONFIG=$HOME/.kube/config" | tee -a ~/.bashrc

# install a pod network (cilium)
curl -L --remote-name-all https://github.com/cilium/cilium-cli/releases/latest/download/cilium-linux-amd64.tar.gz{,.sha256sum}
sha256sum --check cilium-linux-amd64.tar.gz.sha256sum
sudo tar xzvfC cilium-linux-amd64.tar.gz /usr/local/bin
rm cilium-linux-amd64.tar.gz{,.sha256sum}
/usr/local/bin/cilium install

```
