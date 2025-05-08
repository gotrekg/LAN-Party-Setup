# 03 - Black Ops 4 Server VM Setup

Let’s get a Windows 10 LTSC VM up on Proxmox for our Black Ops 4 server using the Shield BO4 docs (https://shield-bo4.gitbook.io/document).

---

## 🔧 VM Configuration at a Glance

| Setting               | Value                                     |
|-----------------------|-------------------------------------------|
| **VM Name**           | `blackops4-server`                        |
| **OS Type/Version**   | Microsoft Windows 10 / 2019               |
| **CPU**               | 4 cores (1 socket)                        |
| **Memory**            | 16 GB                                     |
| **Disk**              | 100 GB thin-provisioned on ZFS pool       |
| **CD/DVD ISOs**       | Windows 10 LTSC ISO<br>VirtIO (virtio-win-0.1.271.iso) |
| **Storage Bus**       | SCSI / VirtIO SCSI single                |
| **Disk Format**       | Raw (on ZFS) or qcow2 for GUI snapshots   |
| **Cache**             | No cache (ZFS default)                    |
| **Discard (TRIM)**    | Enabled                                   |
| **IO Thread**         | Enabled                                   |
| **Network Bridge**    | `vmbr0`                                   |
| **Network Model**     | VirtIO (paravirtualized)                  |
| **CPU Type**          | `host`                                    |
| **VM Best Practices** | https://pve.proxmox.com/wiki/Windows_10_guest_best_practices |

---

## 🖥️ Grab the ISOs

1. **Windows 10 LTSC ISO**  
   Download from Massgrave: https://massgrave.dev/windows_ltsc_links

2. **VirtIO Drivers ISO**  
   Get from the Proxmox wiki QEMU Guest Agent page:  
   https://pve.proxmox.com/wiki/Qemu-guest-agent  
   > I used `virtio-win-0.1.271.iso`

---

## 🌐 Networking

- Bridge to `vmbr0`  
- Model: VirtIO (paravirtualized)  
- Firewall: enabled (optional)

---

## 💻 CPU & Memory

- Sockets: 1  
- Cores: 4  
- Type: `host` (exposes CPU features to VM)  
- Memory: 16 GB

---

## 💾 Disk Settings

- Bus/Device: SCSI with VirtIO SCSI single  
- Storage: your ZFS pool (fast NVMe)  
- Size: 100 GB (thin-provisioned)  
- Format: raw (best performance) or qcow2 (if you want GUI snapshots)  
- Cache: Default (no cache)  
- Discard: ✔️  
- IO Thread: ✔️

---

## 🚀 Fire It Up & Install

1. Create the VM in Proxmox using the settings above, then power it on.
2. Load the VirtIO Drivers ISO in the VM console so Windows can see the virtual drive.
3. Proceed with the Windows 10 LTSC installation.
4. Inside the new Windows install:
   - Install the VirtIO storage & network drivers.
   - Install the QEMU Guest Agent (from the same VirtIO ISO).
   - Install the Visual C++ Redistributable (All‑in‑One from TechPowerUp).
5. Navigate to the Shield BO4 docs (https://shield-bo4.gitbook.io/document/server-setup/server-setup) and follow the server setup steps.
