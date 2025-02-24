---
layout: post
title: Kernel Fix
categories: [ Red Hat, Ansible, ]
tags: [ yum, redhat, satellite ]
show-avatar: false
permalink: kernel-fix
featured: false
hidden: false
titleshort: Kernel Fix
---

## Installing custom kernel with sub packages

- Check all packages installed and available

```shell
$ sudo dnf list kernel --showduplicates
$ sudo dnf list kernel-headers --showduplicates
$ sudo dnf list kernel-devel --showduplicates
```

- Download RPMs manually from [Fedora Koji](https://koji.fedoraproject.org/koji/).

  - `kernel-6.12.4-200.fc41.x86_64.rpm`
  - `kernel-core-6.12.4-200.fc41.x86_64.rpm`
  - `kernel-modules-6.12.4-200.fc41.x86_64.rpm`
  - `kernel-modules-core-6.12.4-200.fc41.x86_64.rpm`
  - `kernel-devel-6.12.4-200.fc41.x86_64.rpm`
  - `kernel-headers-6.12.4-200.fc41.x86_64.rpm`

- Install packages and verify

```shell
$ sudo rpm -Uvh --oldpackage --nodeps kernel-*.rpm
```

- Set the default kernel

```shell
$ sudo grubby --set-default /boot/vmlinuz-6.12.4-200.fc41.x86_64
```

- Reboot (`sudo reboot)

## Exclude kernel from update

- Temporary skip

```shell
$ sudo dnf update --exclude=kernel*
```

- update `/etc/dnf/dnf.conf`

```shell
[main]
...
exclude=kernel-*
```


## KVM issue:

```shell
$ lsmod | grep kvm

# Unload KVM Modules Temporarily
$ sudo rmmod kvm_intel kvm

# Load KVM back
$ sudo modprobe kvm_intel
```


- Update vboxconfig


```shell
$ sudo /sbin/vboxconfig
```