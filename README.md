
# Contrail Virtual Fabric

# 1 Deployment

#### [Contrail Fabric Management](cfm)
#### [Contrail and Kubernetes](kubernetes)

# 2 Hypervisor Host

The whole virtual fabric and servers all stay on a single physical server. In this guide, the server has 32 vCPUs, 256 GB memory and 2TB disk.

CentOS 7.5 and Ubuntu 16.04.3 are validated for the hypervisor host.


## 2.1 Networking

Install packages for Linux bridge.

#### CentOS
```
yum install bridge-utils
```

#### Ubuntu
```
apt-get install bridge-utils
```


## 2.2 KVM Hypervisor

All components run as virtual machine. The following packages are required to build host as a KVM based hypervisor.

#### CentOS
```
yum upgrade kernel
yum install qemu-kvm libvirt virt-install libguestfs-tools libguestfs-xfs
```

Enable and start `libvirtd` service after installation.
```
systemctl enable libvirtd
systemctl start libvirtd
```

#### Ubuntu
```
apt-get upgrade kernel
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

### 2.3.1 Enable nested virtualization on the host

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

For better VM performance, it's recommended to leave a disk partition for libvirt volume (LVM) and launch VM on the volume. Here is an example to enable volume on disk partition.
```
virsh pool-define-as --name lv --type logical --source-dev /dev/sda3
virsh pool-build lv
virsh pool-start lv
virsh pool-autostart lv
```

If no LVM available for libvirt, launching VM on disk file works fine.


## 2.5 Additional packages

`sshpass` is for SSH with password in command line. This is only for initialization. After that, SSH key will be used.

`isc-dhcp-server` is for providing DHCP service for initialization. After that, static address will be configured.

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

Enable SNAT on the host for the cluster to access external.

Ensure `ip_forward` is enabled. Otherwise, enable it in `/etc/sysctl.conf`.
```
net.ipv4.ip_forward = 1
```

To confirm the option.
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

## 5.1 BOM

Image List
* CentOS-7-x86_64-GenericCloud-1805.qcow2, VM base image for Contrail controllers and compute nodes
* cirros-0.4.0-x86_64-disk.img, VM base image for BMS and overlay VM
* junos-vmx-x86-64-18.3R1.9.qcow2, vMX RE VM image
* metadata-usb-re.img, original vMX RE metadata image
* vmxhdd.img, original vMX RE HD image
* vFPC-20180829.img, vMX PFE VM image
* vmx-re-hdd.qcow2, updated vMX RE HD image with initial configuration
* vmx-re.qcow2, updated vMX RE image with initial configuration
* vqfx-re.qcow2, updated vQFX RE image with initial configuration
* vqfx-pfe.qcow2, vQFX PFE VM image

Package List
* contrail-ansible-deployer.tgz, Ansible playbook package downloaded from Juniper site.

File List
* vf, script to build virtual fabric
* vm.func, support functions to build VM
* instances.yaml, configuration file to build Contrail cluster
* command_servers.yml, configuration file to build Contrail Command
* contrail-command, script to build Contrail Command
* configure-poc, script to configure cloud
* haproxy.cfg, sample configuration for HAProxy


## 5.2 Build

```
vf create-link
vf launch-vqfx
vf launch-vmx
```

```
vf configure-vqfx
vf configure-vmx
```
Underlay is done.

```
vf launch-builder
```

```
vf build-cluster
```

Update command_servers.yml and contrail-command.
```
vf install-command
vf launch-command
```

# 4 Post deployment

### Enable HAProxy

This for accessing cluster, like web UI, API, etc.
```
apt-get install haproxy
```


