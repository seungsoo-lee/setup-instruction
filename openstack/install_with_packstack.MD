## Preparation
- 3 vm machines, which are based on VMware ESXi (They can be replaced by physical machines)
- If you use VMware ESXi, accept the ```Promiscuous mode``` from the vSwitchX

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
sudo dnf install -y openstack-packstack
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
```

- Run the following network commands
```
sudo systemctl disable firewalld
sudo systemctl stop firewalld
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
sudo systemctl enable network
sudo dnf install -y openvswitch
sudo sysctl -w net.ipv4.ip_forward=1
sudo echo 1 > /proc/sys/net/ipv4/ip_forward
sudo vi /etc/sysctl.conf
net.ipv4.ip_forward=1
```

- Make ```/etc/sysconfig/network-scripts/ifcfg-br-ex``` resemble:
```
DEVICE=br-ex
DEVICETYPE=ovs
TYPE=OVSBridge
BOOTPROTO=static
IPADDR=172.17.0.110
NETMASK=255.255.255.0
GATEWAY=172.17.0.1
DNS1=168.126.63.1
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
IPADDR=10.0.0.110
NETMASK=255.255.255.0
ONBOOT=yes
```

- Change ifname to ethX
```
sudo vi /etc/default/grub
GRUB_CMDLINE_LINUX="crashkernel=auto resume=/dev/mapper/cl-swap rd.lvm.lv=cl/root rd.lvm.lv=cl/swap net.ifnames=0 rhgb quiet"

sudo grub2-mkconfig  -o /boot/grub2/grub.cfg
sudo reboot
```

## Change the hostname - controller, compute1, compute2, ...
```
# hostnamectl set-hostname controller
```

## Enable ssh login without entering password (From now on, it should be executed on the controller node only)

- set ```/etc/hosts```
```
# vi /etc/hosts
172.17.0.113 controller
172.17.0.114 compute
172.17.0.115 network
```

```
$ su -
# ssh-keygen
# ssh-copy-id root@controller [and ip]
# ssh-copy-id root@compute1 [and ip]
# ssh-copy-id root@compute2 [and ip]
```
- Generate packstack configuration file and edit it
```
# packstack --gen-answer-file=packstack.cfg 
```
- Configure the answer file
```
  76 CONFIG_NTP_SERVERS=time.bora.net
  
  91 CONFIG_CONTROLLER_HOST=172.17.0.110

  94 CONFIG_COMPUTE_HOSTS=172.17.0.111,172.17.0.112

  98 CONFIG_NETWORK_HOSTS=172.17.0.110,172.17.0.111,172.17.0.112
   
  750 CONFIG_NEUTRON_ML2_TYPE_DRIVERS=flat,vxlan
  
  756 CONFIG_NEUTRON_ML2_TENANT_NETWORK_TYPES=vxlan

  824 CONFIG_NEUTRON_OVN_BRIDGE_IFACES=br-ex:eth0
  
  899 CONFIG_NEUTRON_OVN_TUNNEL_IF=eth1
  
  1137 CONFIG_PROVISION_DEMO=n
  
```
- Install OpenStack
```
# packstack --answer-file=packstack.cfg
```
<img src="./images/image4.png" width="70%" height="70%"></img>

# get cirros image

```
curl -L http://download.cirros-cloud.net/0.3.4/cirros-0.3.4-x86_64-disk.img | glance \
         image-create --name='cirros image' --visibility=public --container-format=bare --disk-format=qcow2
```

### References
- https://www.rdoproject.org/install/packstack/
- https://ssup2.github.io/theory_analysis/OpenStack_Network_Neutron/
- https://it-hangil.tistory.com/34
- https://yoonsu.tistory.com/3
