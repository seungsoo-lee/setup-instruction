## Preparation
- 3 vm machines, which are based on VMware ESXi (They can be replaced by physical machines)

> - first vm machine -> controller node + network node
> - second vm machine -> compute node + network node
> - third vm machine -> compute node + network node

<img src="./images/image1.png" width="70%" height="70%"></img>

## Install CentOS Stream 9
- select disk custom partition
(Change partitioning scheme LVM --> standard partition)

<img src="./images/image2.png" width="70%" height="70%"></img>
<img src="./images/image3.png" width="70%" height="70%"></img>
```
/         100 GiB
swap      16 GiB
/boot/efi 100 MiB
/var        (most) // openstack vm instacnes here
```

## Change the hostname - controller, compute1, compute2, ...
```
$ sudo vi /etc/hostname
$ sudo vi /etc/hosts 
$ sudo reboot
```

## Enable root login over SSH
```
$ sudo vi /etc/ssh/sshd_config

#LoginGraceTime 2m
PermitRootLogin yes
#StrictModes yes

$ sudo service sshd restart
```

## Modify centos repo

```
$ sudo vi /etc/yum.repos.d/centos.repo  (comment out metalink, and add baseurl)

[baseos]
#metalink=https://mirrors.centos.org/metalink?repo=centos-baseos-$stream&arch=$basearch&protocol=https,http
baseurl=https://mirror.stream.centos.org/9-stream/BaseOS/$basearch/os/

[appstream]
#metalink=https://mirrors.centos.org/metalink?repo=centos-appstream-$stream&arch=$basearch&protocol=https,http
baseurl=https://mirror.stream.centos.org/9-stream/AppStream/$basearch/os/

[crb]
#metalink=https://mirrors.centos.org/metalink?repo=centos-crb-$stream&arch=$basearch&protocol=https,http
baseurl=https://mirror.stream.centos.org/9-stream/CRB/$basearch/os/
```
** [additional] https://algo79.tistory.com/883 **

## Make sure /etc/environment
```
$ sudo vi /etc/environment
LANG=en_US.UTF-8
LC_ALL=en_US.UTF-8
```
## Install centos-release-openstack-zed
```
sudo dnf update -y
sudo dnf config-manager --enable crb
sudo dnf install -y centos-release-openstack-zed
sudo dnf update -y
```
## Disable the selinux
```
sudo setenforce 0
sudo sed -i 's/=enforcing/=disabled/g' /etc/sysconfig/selinux  # or edit /etc/selinux/config to disabled
sudo sed -i 's/=enforcing/=disabled/g' /etc/selinux/config
```

## Install network-relevant things
```
sudo dnf install network-scripts -y
sudo dnf install -y openvswitch
```
- Make network script files

- Make ```/etc/sysconfig/network-scripts/ifcfg-br-ex``` resemble:
```
DEVICE=br-ex
DEVICETYPE=ovs
TYPE=OVSBridge
BOOTPROTO=static
IPADDR=192.168.122.212
NETMASK=255.255.255.0
GATEWAY=192.168.122.1
DNS1=192.168.122.1
ONBOOT=yes
```
- Make ```/etc/sysconfig/network-scripts/ifcfg-eth0``` resemble (no BOOTPROTO!):
```
DEVICE=eth0
TYPE=OVSPort
DEVICETYPE=ovs
OVS_BRIDGE=br-ex
ONBOOT=yes
```

- Make ```/etc/sysconfig/network-scripts/ifcfg-eth1```
```
DEVICE=eth1
BOOTPROTO=static
IPADDR=192.168.0.58
NETMASK=255.255.255.0
ONBOOT=yes
```

- Run the following network commands
```
sudo systemctl disable firewalld
sudo systemctl stop firewalld
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
sudo systemctl enable network
```
- Change ifname to ethX
```
sudo vi /etc/default/grub
GRUB_CMDLINE_LINUX="crashkernel=auto resume=/dev/mapper/cl-swap rd.lvm.lv=cl/root rd.lvm.lv=cl/swap net.ifnames=0 rhgb quiet"

sudo grub2-mkconfig  -o /boot/grub2/grub.cfg
sudo reboot
```

## Enable ssh login without entering password (From now on, it should be executed on the controller node only)
```
$ su -
# ssh-keygen
# ssh-copy-id root@controller [and ip]
# ssh-copy-id root@compute1 [and ip]
# ssh-copy-id root@compute2 [and ip]
```
- Install network-scripts and openvswitch
```
# dnf install -y openstack-packstack
# packstack --gen-answer-file=packstack.cfg (then, configure the file)
```
- Install OpenStack
```
# packstack --answer-file=packstack.cfg
```
<img src="./images/image4.png" width="70%" height="70%"></img>

### References
- https://www.rdoproject.org/install/packstack/
- https://ssup2.github.io/theory_analysis/OpenStack_Network_Neutron/
- https://it-hangil.tistory.com/34
- https://yoonsu.tistory.com/3
