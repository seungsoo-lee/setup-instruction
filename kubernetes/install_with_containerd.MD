# Kubernetes Installation with ContainerD on Ubuntu 22.04

## Set hostname and add entries in the hosts file

- On the master node, run
```sh
sudo hostnamectl set-hostname "master"
bash
```
- On the worker nodes, run
```
sudo hostnamectl set-hostname "worker1"   // 1st worker node
sudo hostnamectl set-hostname "worker2"   // 2nd worker node
bash
```
- Add the following entries in ```/etc/hosts``` file on each node
```
192.168.1.173   master
192.168.1.174   worker1
192.168.1.175   worker2
```

## Disable swap & add kernel settings
```bash
# swapp off
sudo swapoff -a
sudo sed -i '/swap.img/s/^/#/' /etc/fstab # (Ubuntu 22.04)

# Kernel Forwarding
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sudo sysctl --system
```

## Install containerd run time
- In this guide, we are using containerd run time for our Kubernetes cluster. So, to install containerd, first install its dependencies.
```
$ sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
```
- Enable docker repository
```
$ sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
- Now, run following apt command to install containerd
```
$ sudo apt update
$ sudo apt install -y containerd.io
```
- Configure containerd so that it starts using systemd as cgroup.
```
$ containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
$ sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
```
- Restart and enable containerd service
```
$ sudo systemctl restart containerd
$ sudo systemctl enable containerd
```

## Add apt repository for Kubernetes
- Execute following commands to add apt repository for Kubernetes
```
$ sudo curl -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://dl.k8s.io/apt/doc/apt-key.gpg
$ sudo echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

##curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
##sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
```
* Note: At time of writing this guide, Xenial is the latest Kubernetes repository but when repository is available for Ubuntu 22.04 (Jammy Jellyfish) then you need replace xenial word with ‘jammy’ in ‘apt-add-repository’ command.

## Install Kubernetes components Kubectl, kubeadm & kubelet
- Install specific version
```
$ sudo apt update
$ apt-cache madison kubectl | grep 1.28
   kubectl |  1.28.0-00 | http://apt.kubernetes.io kubernetes-xenial/main amd64 Packages
   ...
$ sudo apt install -y kubelet=1.28.0-00 kubeadm=1.28.0-00 kubectl=1.28.0-00
```
- Install Kubernetes components like kubectl, kubelet and Kubeadm utility on all the nodes. Run following set of commands,
```
$ sudo apt update
$ sudo apt install -y kubelet kubeadm kubectl
$ sudo apt-mark hold kubelet kubeadm kubectl
```

## [Master Node] Initialize Kubernetes cluster with Kubeadm command
- Now, we are all set to initialize Kubernetes cluster. Run the following Kubeadm command from the master node only.
```
$ sudo kubeadm init --control-plane-endpoint=master --pod-network-cidr=192.168.0.0/16
```
Calio: ```--pod-network-cidr=192.168.0.0/16```
Flannel: ```--pod-network-cidr=10.244.0.0/16```
Cilium: ``` don't care```
- So, to start interacting with cluster, run following commands from the master node,
```
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
- Now, try to run following kubectl commands to view cluster and node status
```
$ kubectl cluster-info
$ kubectl get nodes
```
## [Worker Node] Join to the cluster
- Join both the worker nodes to the cluster, command is already there is output, just copy paste on the worker nodes,
```
$ sudo kubeadm join k8smaster.example.net:6443 --token vt4ua6.wcma2y8pl4menxh2 \
   --discovery-token-ca-cert-hash sha256:0494aa7fc6ced8f8e7b20137ec0c5d2699dc5f8e6166
```
- As we can see nodes status is ‘NotReady’, so to make it active. We must install CNI (Container Network Interface) or network add-on plugins like Calico, Flannel and Weave-net.

## Delete Kubernetes
```
sudo kubeadm reset
kubectl config delete-context kubernetes-admin@kubernetes
sudo rm -rf $HOME/.kube
sudo rm $HOME/k8s_init.log
```
### References
- https://computingforgeeks.com/install-kubernetes-cluster-ubuntu-jammy/
