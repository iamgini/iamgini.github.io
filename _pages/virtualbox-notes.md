---
layout: post
title: VirtualBox Notes
categories: [ openshift ]
tags: [ OpenShift Installation ]
show-avatar: false
permalink: /virtualbox
featured: false
hidden: false
titleshort: virtualbox
---

- [1. VirtualBox Guest Addtion on CLI](#1-virtualbox-guest-addtion-on-cli)
- [2. Optimize VirtualBox VM Disk Space](#2-optimize-virtualbox-vm-disk-space)
- [3. Troubleshooing VirtualBox VM](#3-troubleshooing-virtualbox-vm)
  - [3.1. Unable to mount Shared folders in CLI](#31-unable-to-mount-shared-folders-in-cli)

## 1. VirtualBox Guest Addtion on CLI

- Mount Guest Addtion ISO (from Console Menu)
- Mount it from OS (/vmboxguestiso is destination mount point)
```
mount /dev/cdrom /vmboxguestiso/
```
- Resolve Dependancies

**Install kernel-header**

```
`sudo yum update && sudo yum -y install kernel-headers kernel-devel`

## install other packages
yum install gcc make perl
```

## 2. Optimize VirtualBox VM Disk Space

1. Clean up inside the VM

```shell
$ sudo dnf clean all
$ sudo journalctl --vacuum-time=7d
$ sudo rm -rf /var/cache/*
$ sudo rm -rf ~/.cache/*
```

2. Zero out the free space

```shell
$ sudo dd if=/dev/zero of=/zerofile bs=1M
dd: error writing '/zerofile': No space left on device
107617+0 records in
107616+0 records out
112843751424 bytes (113 GB, 105 GiB) copied, 127.059 s, 888 MB/s

$ sudo rm -f /zerofile
```

3. Shutdown the VM

```shell
$ sudo shutdown -h now
```

4. Run VBoxManage to compact the disk

VirtualBox does not support compacting .vmdk disks directly. Convert your .vmdk disk to `.vdi`, compact it, and then continue using the compacted disk.

```shell
 $  VBoxManage clonehd \
  "/path/to/vm-folder/aap25-rhel92-1/generic-rhel9-virtualbox-disk001.vmdk" \
  "/path/to/vm-folder/aap25-rhel92-1/aap25-compact.vdi" \
  --format VDI
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
Clone medium created in format 'VDI'. UUID: 6517df59-9cd0-4393-bb03-c5d182219cc3
```

Now run the compact `vdi` command.

```shell
$  VBoxManage modifymedium "/home/gmadappa/VirtualBox VMs/aap25-rhel92-1/aap25-compact.vdi" --compact
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
```


## 3. Troubleshooing VirtualBox VM

### 3.1. Unable to mount Shared folders in CLI

Check vboxsf module
`sudo modprobe vboxsf`
