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
sudo nano /etc/apt/sources.list.d/backports.list
sudo apt update
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

### 4. Verify ZFS Installation

After installing ZFS, check that it is installed and running correctly:

```bash
zfs version
lsmod | grep zfs
```

You should see output similar to:

```
user@lancache:~$ zfs version
zfs-2.3.2-2~bpo12+1
zfs-kmod-2.3.2-2~bpo12+1

user@lancache:~$ lsmod | grep zfs
zfs                  5902336  0
spl                   143360  1 zfs

```

## 🛠️ ZFS Pool Setup

> **⚠️ IMPORTANT:**  
> **Double-check your drive names before wiping or creating pools!**  
> Using the wrong device could destroy your OS or important data.  
> For example, in my setup, `/dev/nvme0n1` is the OS drive:
>
> ```
> NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
> sda           8:0    0 465.8G  0 disk
> sdb           8:16   0 465.8G  0 disk
> sdc           8:32   1 115.2G  0 disk
> ├─sdc1        8:33   1 115.2G  0 part
> └─sdc2        8:34   1    32M  0 part
> sr0          11:0    1  1024M  0 rom
> nvme1n1     259:0    0 953.9G  0 disk
> nvme2n1     259:1    0 953.9G  0 disk
> nvme0n1     259:2    0 476.9G  0 disk
> ├─nvme0n1p1 259:3    0   512M  0 part /boot/efi
> ├─nvme0n1p2 259:4    0 475.5G  0 part /
> └─nvme0n1p3 259:5    0   976M  0 part [SWAP]
> ```
>
> **Never wipe or use your OS drive for ZFS pools!**  
> Always confirm which drives are safe to use with `lsblk` or `fdisk -l`.

Before creating any ZFS pools, wipe the drives you plan to use (this will erase all data on them):

```bash
sudo wipefs -a /dev/nvme1n1
sudo wipefs -a /dev/nvme2n1
sudo wipefs -a /dev/sda
sudo wipefs -a /dev/sdb
```

### 🔹 LANCache Pool

**Drives**: `nvme1n1`, `nvme2n1`  
**Layout**: **RAID 0**  
- RAID 0 for performance

```bash
sudo zpool create  lancache /dev/nvme1n1 /dev/nvme2n1
```

### 🔹 Shared Folder Pool

**Drives**: `sda`, `sdb`  
**Layout**: **Mirror**  
- Classic 1:1 mirror — simple and safe

```bash
sudo zpool create shared mirror /dev/sda /dev/sdb
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

This way people can drop games, patches, or whatever 
