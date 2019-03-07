
# Contrail Virtual Fabric with vQFX and vMX

Contrail 5.0.2 Fabric Management

# 1 Topology

```
   +-------------+
   |   client    |
   | 172.16.1.10 |
   +-------------+
          |
        br-ext
          |
  +-----------------+-----------------+  +-----------------+-----------------+
  |  xe-0/0/0       |  xe-0/0/1       |  |  xe-0/0/0       |  xe-0/0/1       |
  |  172.16.1.254   |                 |  |                 |                 |
  |                                   |  |                                   |
  | vmx-gw1                           |  | vmx-gw2                           |
  |  em0: 10.6.8.31                   |  |  em0: 10.6.8.32                   |
  |  lo0: 10.6.0.31                   |  |  lo0: 10.6.0.32                   |
  |  ASN: 64031                       |  |  ASN: 64032                       |
  |                                   |  |                                   |
  |  10.6.30.1/30   |  10.6.30.5/30   |  |  10.6.30.9/30   |  10.6.30.13/30  |
  |  xe-0/0/2       |  xe-0/0/3       |  |  xe-0/0/2       |  xe-0/0/3       |
  +-----------------+-----------------+  +-----------------+-----------------+
        |                  |                   |                 |
        |               +--(-------------------+                 |
        |               |  |                                     |
        |               |  +-------------------+                 |
        |               |                      |                 |
  +-----------------+-----------------+  +-----------------+-----------------+
  |  xe-0/0/0       |  xe-0/0/1       |  |  xe-0/0/0       |  xe-0/0/1       |
  |  10.6.30.2/30   |  10.6.30.10/30  |  |  10.6.30.4/30   |  10.6.50.14/30  |
  |                                   |  |                                   |
  | vqfx-spine1                       |  | vqfx-spine2                       |
  |  em0: 10.6.8.21                   |  |  em0: 10.6.8.22                   |
  |  lo0: 10.6.0.21                   |  |  lo0: 10.6.0.22                   |
  |  ASN: 64021                       |  |  ASN: 64022                       |
  |                                   |  |                                   |
  |  10.6.20.1/30   |  10.6.20.5/30   |  |  10.6.20.9/30   |  10.6.20.13/30  |
  |  xe-0/0/2       |  xe-0/0/3       |  |  xe-0/0/2       |  xe-0/0/3       |
  +-----------------+-----------------+  +-----------------+-----------------+
        |                  |                   |                 |
        |               +--(-------------------+                 |
        |               |  |                                     |
        |               |  +-------------------+                 |
        |               |                      |                 |
  +----------------+------------------+  +-----------------------------------+
  |  xe-0/0/0      |  xe-0/0/1        |  |  xe-0/0/0       |  xe-0/0/1       |
  |  10.6.20.2/30  |  10.6.20.10/30   |  |  10.6.20.4/30   |  10.6.20.14/30  |
  |                                   |  |                                   |
  | vqfx-leaf1                        |  | vqfx-leaf2                        |
  |  em0: 10.6.8.11                   |  |  em0: 10.6.8.12                   |
  |  lo0: 10.6.0.11                   |  |  lo0: 10.6.0.12                   |
  |  ASN: 64011                       |  |  ASN: 64012                       |
  |                                   |  |                                   |
  |  xe-0/0/4  |  xe-0/0/2 | xe-0/0/3 |  |  xe-0/0/2  |  xe-0/0/3            |
  +------------+-----------+----------+  +------------+----------------------+
       |              |         |              |            |
       |              |         |              |            |
     br-r1        +-------+     |          +-------+        |
       |          | bms12 |     |          | bms22 |        |
       |          +-------+     |          +-------+        |
       |                        |                           |
  +-----------------------+   +-------------------------------+
  | openstack   10.6.8.1  |   |          bms-dh               |
  | contrail    10.6.8.2  |   +-------------------------------+
  | csn         10.6.8.3  |
  | compute     10.6.8.4  |
  | command     10.6.8.10 |
  +-----------------------+
       |                         management: 10.6.8.0/24
     br-int                      loopback:   10.6.0.0/24
   10.6.8.254                    spine-leaf: 10.6.20.0/24 10.6.30.0/24
       |                         rack-1:     10.6.11.0/24
    HAProxy                      rack-2:     10.6.12.0/24

  Contrail web UI:   https://<host>:8143
  Contrail Command:  https://<host>:9091
  OpenStack Horizon: http://<host>
```


## Resource
```
                  vCPU    memory(GB)    disk(GB)    OS
command             2        32            100      CentOS 7.5-1805
openstack           4        64            100      CentOS 7.5-1805
contrail            4        64            100      CentOS 7.5-1805
csn                 1        16             80      CentOS 7.5-1805
compute             4        32            100      CentOS 7.5-1805
vqfx-leaf1-re       1         1                     Junos 18.1
vqfx-leaf1-pfe      1         2                     Junos 18.1
vqfx-leaf2-re       1         1                     Junos 18.1
vqfx-leaf2-pfe      1         2                     Junos 18.1
vqfx-spine1-re      1         1                     Junos 18.1
vqfx-spine11-pfe    1         2                     Junos 18.1
vqfx-spine1-re      1         1                     Junos 18.1
vqfx-spine11-pfe    1         2                     Junos 18.1
vmx-gw1-vcp         1         1                     Junos 18.3R1
vmx-gw1-vfp         4         2                     Junos 18.3R1
vmx-gw1-vcp         1         1                     Junos 18.3R1
vmx-gw1-vfp         4         2                     Junos 18.3R1
bms12               1         1                     Cirros 0.4.0
bms22               1         1                     Cirros 0.4.0
bms-dh              1         1                     CentOS 7.5-1805
----------------------------------------------------------------
Total              36       214
```


# 2 Host

The whole virtual fabric stays on single physical server. In this guide, the server has 32 vCPUs, 256 GB memory and 2TB disk.

CentOS 7.5 and Ubuntu 16.04.3 are validated.


## 2.1 Networking

Install packages for brdge and bond interface.

#### CentOS
```
apt-get install bridge-utils
```

#### Ubuntu
```
apt-get install bridge-utils ifenslave
```


## 2.2 Hypervisor

All components run as virtual machine. The following packages are required to build host as a KVM based hypervisor.

#### CentOS
```
yum install qemu-kvm libvirt virt-install libguestfs-tools libguestfs-xfs
```

Enable and start `libvirtd` service.
```
systemctl enable libvirtd
systemctl start libvirtd
```

#### Ubuntu
```
apt-get install qemu-kvm libvirt-bin virtinst libguestfs-tools
```

Update /etc/apparmor.d/abstractions/libvirt-qemu to allow access to volume devices.
```
  network inet stream,
  network inet6 stream,

+ /dev/dm* rw,
  /dev/net/tun rw,
  /dev/tap* rw,
```

Patch libguestfs as Appendix B.1.


## 2.3 Nested virtualization

### 2.3.1 Enable nested virtualization

Create `/etc/modprobe.d/kvm-nested.conf`.
```
options kvm-intel nested=1
options kvm-intel enable_shadow_vmcs=1
options kvm-intel enable_apicv=1
options kvm-intel ept=1
```

Reload KVM kernel module. Ensure no VM running.
```
modprobe -r kvm_intel
modprobe -a kvm_intel
```

To check nested virtualization option.
```
# cat /sys/module/kvm_intel/parameters/nested
Y
```


### 2.3.2 Enable hypervisor features on VM

When use virt-install, add option `--cpu host`.

Or update VM XML with the followings.
```
  <cpu mode='host-model' check='partial'>
    <model fallback='allow'/>
  </cpu>
```

To check KVM support on VM.
```
# lsmod | grep kvm
kvm_intel             174841  3 
kvm                   578558  1 kvm_intel
```


## 2.4 Storage

For better VM performance, it's recommended to leave a disk partition for libvirt volume. Here is an example.
```
virsh pool-define-as --name lv --type logical --source-dev /dev/sda3
virsh pool-build lv
virsh pool-start lv
virsh pool-autostart lv
```


## 2.5 Additional packages

`sshpass` is for SSH to vQFX with password in command line. This is only for initialization. After that, SSH key will be used.

`isc-dhcp-server` is for providing DHCP service to vQFX for initialization. After that, static address will be configured on vQFX.

#### Ubuntu
```
apt-get install sshpass isc-dhcp-server
```

#### CentOS
```
yum install sshpass dhcp
```

Stop firewalld.
```
systemctl stop firewalld
systemctl disable firewalld
```

## 2.6 SSH key
Generate SSH key or copy existing key.
```
ssh-keygen
```

## 2.7 Underlay links

### 2.7.1 Linux bridge
Doesn't forward LACP packet, can't validate BMS duel-homeing.

### 2.7.2 macvtap on veth
Better performance.

Note, the MAC in VM has to match the MAC on macvtap interface. For vQFX, it doesn't take the MAC from NIC, have to set MAC in configuration.

Note, have to upgrade kernel to at least 3.10.0-957.5.1.el7.x86_64 to avoid an issue that veth link slows down after sometime.


## 2.8 SNAT

Enable SNAT on the host for the cluster to access external/internet.

Ensure `ip_forward` is enabled. Otherwise, enable it in `/etc/sysctl.conf`.
```
cat /proc/sys/net/ipv4/ip_forward
```

Add `iptables` rule into NAT table.
```
iptables -t nat -A POSTROUTING -o <nic> -j SNAT --to <address>
```
`nic` is the interface having external connectivity. `address` is the address on `nic`.


# 3 vMX

vMX PFE requires hugepage.


# 4 vQFX

[vQFX trial](https://www.juniper.net/us/en/dm/free-vqfx-trial/)

[vQFX image download](http://www.juniper.net/support/downloads/?p=vqfxeval#sw)

* Version 18.4, VQFX10K RE Disk Image (qcow2), jinstall-vqfx-10-f-18.4R1.8.qcow2
* Version 17.4, VQFX10K PFE Disk Image, cosim_20180212.qcow2

RE VM supports virtio NIC. PFE VM has to be e1000 NIC.

Customize the origin RE VM to add configuration for root password, SSH and NETCONF services, and DHCP on em0 interface.


# 5 Build virtual fabric

Images
```
CentOS-7-x86_64-GenericCloud-1805.qcow2
cirros-0.4.0-x86_64-disk.img
junos-vmx-x86-64-18.3R1.9.qcow2
metadata-usb-re.img
vFPC-20180829.img
vmxhdd.img
vmx-re-hdd.qcow2
vmx-re.qcow2
vqfx-pfe.qcow2
vqfx-re.qcow2
```

```
vf create-bridge
vf launch-vqfx
vf launch-vmx
```

```
vf configure-vqfx
vf configure-vmx
```
Underlay is done.

``
vf launch-builder
```

```
vf build-cluster
vf install-command
vf launch-command
```

# 4 Post deployment

### Enable HAProxy

This for accessing cluster, like web UI, API, etc.
```
apt-get install haproxy
```


