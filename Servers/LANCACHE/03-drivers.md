# 03 - Disk Prep & ZFS Layout

For this build, the system has been updated with **3 NVMe drives** and **2 SATA SSDs**. Here's the new layout:

## 💾 Drive Overview

- **nvme2n1** — Boot drive (Debian OS)  
- **nvme0n1, nvme1n1** — Available for LANCache storage (RAID 0 for performance)
- **sda, sdb** — Available for shared user folder (drop-in games, files, etc.)

---

## 🧰 Installing ZFS (Bookworm Backports)

Debian includes ZFS in the contrib repo, but for newer versions it’s better to use the **backports**. Here's how to set it up properly:

### 1. Add the backports repo

Create a new file for the backports repository:

```bash
sudo nano /etc/apt/sources.list.d/bookworm-backports.list
```

Paste this in:

```
deb http://deb.debian.org/debian bookworm-backports main contrib
deb-src http://deb.debian.org/debian bookworm-backports main contrib
```

### 2. Pin the backport priority

```bash
sudo nano /etc/apt/preferences.d/90_zfs
```

Add this content:

```
Package: src:zfs-linux
Pin: release n=bookworm-backports
Pin-Priority: 990
```

### 3. Install ZFS

```bash
sudo apt update
sudo apt install dpkg-dev linux-headers-generic linux-image-generic
sudo apt install zfs-dkms zfsutils-linux
```

You should now have ZFS ready to roll.

## 🛠️ ZFS Pool Setup

### 🔹 LANCache Pool

**Drives**: `nvme0n1`, `nvme1n1`  
**Layout**: **RAID 0**  
- RAID 0 for performance

```bash
sudo zpool create lancache raidz /dev/nvme0n1 /dev/nvme1n1
```

### 🔹 Shared Folder Pool

**Drives**: `sda`, `sdb`  
**Layout**: **Mirror**  
- Classic 1:1 mirror — simple and safe

```bash
zpool create shared mirror /dev/sda /dev/sdb
```

## 🔒 Permissions Setup

Once the pools are created, adjust ownership and permissions:

### For LANCache (read-only for everyone else)

```bash
sudo zfs create lancache/data
sudo chown root:root /lancache/data
sudo chmod 555 /lancache/data  # Read and execute only
```

This prevents anything (like someone’s antivirus) from accidentally deleting files in the cache.

### For Shared Folder (read/write access)

```bash
sudo zfs create shared/public
sudo chown nobody:nogroup /shared/public
sudo chmod 777 /shared/public  # Full access for anyone on the LAN
```

This way people can drop games, patches, or whatever into the share freely.
