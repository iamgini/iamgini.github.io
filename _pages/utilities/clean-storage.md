# 🧹 Fixing Full `/` Root Partition on Fedora

If your root (`/`) partition is nearly full but `/home` has plenty of space, follow these steps to clean up and free disk space.

---

## 📊 Quick Stats Example

```bash
$ df -h /
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/fedora-root   49G   46G  682M  99%
```

---

## ✅ Step-by-Step Cleanup

### 1. Check What’s Taking Space

```bash
sudo du -ahx / | sort -rh | head -n 30
```

This shows the top 30 space consumers on the root filesystem, excluding mounted ones like `/home`.

---

### 2. Clean DNF/YUM Cache

```bash
sudo dnf clean all
```

---

### 3. Remove Old Kernel Versions

List installed kernels:

```bash
rpm -q kernel
```

Remove older versions (keep the latest 2):

```bash
sudo dnf remove kernel-<version>
```

---

### 4. Vacuum Journal Logs

Check journal size:

```bash
sudo journalctl --disk-usage
```

Reduce journal size:

```bash
sudo journalctl --vacuum-size=500M
```

---

### 5. Remove ABRT Crash Dumps

```bash
sudo rm -rf /var/spool/abrt/*
```

These are crash reports, safe to delete if you don't need diagnostics.

---

### 6. Flatpak Cleanup

If not using Flatpak apps:

```bash
sudo flatpak uninstall --unused
sudo rm -rf /var/lib/flatpak
```

---

### 7. Docker Cleanup

To remove all unused Docker data:

```bash
docker system prune -a
```

⚠️ Make sure you don’t need the old images/containers.

---

### 8. Optional: Move Big Files to `/home`

If any large files in `/` can be relocated:

```bash
sudo mv /path/to/largefile /home/<youruser>/
```

---

## 📌 Recap: Major Cleanup Targets

| Path                         | Description                |
|------------------------------|----------------------------|
| `/var/lib/flatpak`           | Flatpak apps & runtimes    |
| `/var/log`                   | System logs (journald)     |
| `/var/spool/abrt`            | Crash dumps                |
| `/var/lib/docker`            | Docker data                |
| `/var/cache`                 | Package caches             |

---

Stay tidy, automate if possible, and watch for disk usage alerts! 🚀
