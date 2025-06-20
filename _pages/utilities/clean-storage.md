---
layout: post
title: Cleaning Linux Storage
author: gini
categories: [ linux, storage ]
# image: "assets/images/2021/pexels-rfstudio-3060324-books.jpg"
tags: [linux]
permalink: /clean-disk
featured: false
hidden: false
showindex: true
titleshort: clean-disk
---

- [Fixing Full `/` Root Partition on Fedora](#fixing-full--root-partition-on-fedora)
  - [📊 Quick Stats Example](#-quick-stats-example)
  - [✅ Step-by-Step Cleanup](#-step-by-step-cleanup)
    - [1. Check What’s Taking Space](#1-check-whats-taking-space)
    - [2. Clean DNF/YUM Cache](#2-clean-dnfyum-cache)
    - [3. Remove Old Kernel Versions](#3-remove-old-kernel-versions)
    - [4. Vacuum Journal Logs](#4-vacuum-journal-logs)
    - [5. Remove ABRT Crash Dumps](#5-remove-abrt-crash-dumps)
    - [6. Flatpak Cleanup - Optional](#6-flatpak-cleanup---optional)
    - [7. Podman and Docker Cleanup](#7-podman-and-docker-cleanup)

Major Cleanup Targets

| Path                         | Description                |
|------------------------------|----------------------------|
| `/var/lib/flatpak`           | Flatpak apps & runtimes    |
| `/var/log`                   | System logs (journald)     |
| `/var/spool/abrt`            | Crash dumps                |
| `/var/lib/docker`            | Docker data                |
| `/var/cache`                 | Package caches             |

# Fixing Full `/` Root Partition on Fedora

If your root (`/`) partition is nearly full but `/home` has plenty of space, follow these steps to clean up and free disk space.

## 📊 Quick Stats Example

```bash
$ df -h /
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/fedora-root   49G   46G  682M  99%
```

## ✅ Step-by-Step Cleanup

### 1. Check What’s Taking Space

```bash
sudo du -ahx / | sort -rh | head -n 30
```

This shows the top 30 space consumers on the root filesystem, excluding mounted ones like `/home`.

### 2. Clean DNF/YUM Cache

```bash
sudo dnf clean all
```

### 3. Remove Old Kernel Versions

List installed kernels:

```bash
rpm -q kernel
```

Remove older versions (keep the latest 2):

```bash
sudo dnf remove kernel-<version>
```

### 4. Vacuum Journal Logs

Check journal size:

```bash
sudo journalctl --disk-usage
```

Reduce journal size:

```bash
sudo journalctl --vacuum-size=500M
```

### 5. Remove ABRT Crash Dumps

```bash
sudo rm -rf /var/spool/abrt/*
```

These are crash reports, safe to delete if you don't need diagnostics.


### 6. Flatpak Cleanup - Optional

If NOT using Flatpak apps:

```bash
sudo flatpak uninstall --unused
sudo rm -rf /var/lib/flatpak
```


### 7. Podman and Docker Cleanup

To remove all unused Docker data:

```bash
docker system prune -a
# or
podman system prune -a
```

⚠️ Make sure you don’t need the old images/containers.
