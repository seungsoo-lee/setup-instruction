## Set hostname and add entries in the hosts file

- On the master node, run
```sh
$ sudo hostnamectl set-hostname "master"
$ exec bash
```
- On the worker nodes, run
```
$ sudo hostnamectl set-hostname "worker1"   // 1st worker node
$ sudo hostnamectl set-hostname "worker2"   // 2nd worker node
$ exec bash
```
- Add the following entries in ```/etc/hosts``` file on each node
```
192.168.1.173   master
192.168.1.174   worker1
192.168.1.175   worker2
```

## Disable swap & add kernel settings
- Execute beneath swapoff and sed command to disable swap. Make sure to run the following commands on all the nodes.
```
$ sudo swapoff -a
$ sudo sed -i '/swap.img/s/^/#/' /etc/fstab # (Ubuntu 22.04)
$ sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```
- Load the following kernel modules on all the nodes,
```
$ sudo tee /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF
$ sudo modprobe overlay
$ sudo modprobe br_netfilter
```
- Set the following Kernel parameters for Kubernetes, run beneath tee command
```
$ sudo tee /etc/sysctl.d/kubernetes.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
```
- Reload the above changes, run
```
$ sudo sysctl --system
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
$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
$ sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
```
* Note: At time of writing this guide, Xenial is the latest Kubernetes repository but when repository is available for Ubuntu 22.04 (Jammy Jellyfish) then you need replace xenial word with ‘jammy’ in ‘apt-add-repository’ command.

## Install Kubernetes components Kubectl, kubeadm & kubelet
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
