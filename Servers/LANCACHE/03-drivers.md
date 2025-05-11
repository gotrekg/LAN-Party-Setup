# 03 - Disk Prep & ZFS Layout

For this build, I’ve got a total of **5 NVMe drives** — one in the motherboard slot (used for boot), and four more connected via PCIe adapters. Plus, I’ve got **2 SATA SSDs** for shared files.

## 💾 Drive Overview

- **nvme0n1** — Boot drive (Debian OS)
- **nvme1n1, nvme2n1, nvme3n1, nvme4n1** — Available for LANCache storage
- **sda, sdb** — Available for shared user folder (drop-in games, files, etc.)

## 🧰 Installing ZFS (Bookworm Backports)

Debian includes ZFS in the contrib repo, but for newer versions it’s better to use the **backports**. Here's how to set it up properly:

### 1. Add the backports repo

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
apt update
apt install dpkg-dev linux-headers-generic linux-image-generic
apt install zfs-dkms zfsutils-linux
```

You should now have ZFS ready to roll.

## 🛠️ ZFS Pool Setup

### 🔹 LANCache Pool

**Drives**: `nvme1n1`, `nvme2n1`, `nvme3n1`, `nvme4n1`  
**Layout**: **RAIDZ1**  
- One drive worth of redundancy
- Good balance of speed and safety for read-heavy cache workloads

```bash
zpool create lancache raidz1 /dev/nvme1n1 /dev/nvme2n1 /dev/nvme3n1 /dev/nvme4n1
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
zfs create lancache/data
chown root:root /lancache/data
chmod 555 /lancache/data  # Read and execute only
```

This prevents anything (like someone’s antivirus) from accidentally deleting files in the cache.

### For Shared Folder (read/write access)

```bash
zfs create shared/public
chown nobody:nogroup /shared/public
chmod 777 /shared/public  # Full access for anyone on the LAN
```

This way people can drop games, patches, or whatever into the share freely.
