---
layout: post
title: RHEL Local Reppo
categories: [ Red Hat, Ansible, ]
tags: [ yum, redhat, satellite ]
show-avatar: false
permalink: rhel-repo
featured: false
hidden: false
titleshort: rhel-repo
---

Download RHEL - https://access.redhat.com/downloads/content/rhel

```shell
sudo mkdir -p  /media/rheldvd

sudo mount /dev/sr0  /media/rheldvd


# cat /etc/yum.repos.d/my.repo
[dvd-BaseOS]
name=DVD for RHEL - BaseOS
baseurl=file:///media/rheldvd/BaseOS
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release

[dvd-AppStream]
name=DVD for RHEL - AppStream
baseurl=file:///media/rheldvd/AppStream
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release

# Disable RHEL repos
sudo subscription-manager repos --disable=rhel-8-for-x86_64-appstream-rpms
sudo subscription-manager repos --disable=rhel-8-for-x86_64-baseos-rpms

sudo yum clean all
sudo yum repolist enabled
```


References:
- [Need to set up yum repository for locally-mounted DVD on Red Hat Enterprise Linux 8](https://access.redhat.com/solutions/3776721)