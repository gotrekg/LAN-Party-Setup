# 04 - Black Ops 3 Server Template (Debian 12)

This guide shows how I build a Debian 12 template in Proxmox for a Black Ops 3 dedicated server. It’s a bit more involved than the BO4 Windows VM, so we’ll cover every step in detail.

---

## 🛠️ 1. Create the Base VM

- **Name:** `Black-ops-3-template`  
- **CPU:** 2 cores  
- **Memory:** 8 GB  
- **Disk:** 30 GB (thin on ZFS)  
- **ISO:** Attach Debian 12 netinst (minimal) ISO  
- **Network:** Bridge `vmbr0`, model VirtIO  
- **Install:** Power on and complete a headless Debian install  
  - Enable **SSH server** (no desktop)  
  - Hostname can be anything; clones will rename themselves  

---

## 🔌 2. Initial SSH & System Prep

1. SSH into your new VM as `masarik`:  
   ```bash
   ssh masarik@<template_ip>
   ```

2. **Enable passwordless sudo**:  
   ```bash
   sudo visudo
   # add:
   masarik ALL=(ALL) NOPASSWD: ALL
   ```

3. **Update & install essentials**:  
   ```bash
   sudo apt update && sudo apt upgrade -y
   sudo apt install -y qemu-guest-agent git screen unzip
   ```

4. **Reboot** to activate the guest agent:  
   ```bash
   sudo reboot
   ```

---

## 📥 3. Install BO3 Server Files

1. **Clone the installer repo**:  
   ```bash
   git clone https://github.com/gotrekg/BlackOps3ServerInstaller.git
   cd BlackOps3ServerInstaller
   ```

2. **Download game files**:  
   ```bash
   chmod +x server_files_downloader_debian.sh
   ./server_files_downloader_debian.sh
   ```

3. **Make the server starter script executable**:  
   ```bash
   chmod +x BOIII_Server.sh
   ```

4. **Edit the server config as needed:**  
   - Edit `zone/server.cfg`, `server_zm.cfg`, or `server_cp.cfg` (defaults work out of the box).

---

## ⚙️ 4. IW4M-Admin Web UI

IW4M-Admin provides a web frontend for RCON and server management.

1. **Install .NET 8 SDK**:  
   ```bash
   wget https://packages.microsoft.com/config/debian/12/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
   sudo dpkg -i packages-microsoft-prod.deb
   rm packages-microsoft-prod.deb
   sudo apt-get update
   sudo apt-get install -y dotnet-sdk-8.0
   ```

2. **Download & extract IW4M-Admin**:  
   ```bash
   wget https://github.com/RaidMax/IW4M-Admin/releases/download/2025.3.22.2-prerelease/IW4MAdmin-2025.3.22.2.zip
   unzip IW4MAdmin-*.zip -d IW4MAdmin
   cd IW4MAdmin
   chmod +x StartIW4MAdmin.sh
   ```

3. **Run the setup**:  
   ```bash
   ./StartIW4MAdmin.sh
   ```
   - Follow prompts (enable web UI, set RCON, choose BO3 parser, etc.).

---

## 🔄 5. Automate Startup (Optional)

Create a quick script (`start-servers.sh`) to launch both services:

```bash
#!/bin/bash
# BO3 server
screen -dmS BO3_Server bash -c "cd ~/BlackOps3ServerInstaller/UnrankedServer && ./BOIII_Server.sh"
# IW4M-Admin
screen -dmS IW4MAdmin bash -c "cd ~/IW4MAdmin && ./StartIW4MAdmin.sh"
```

Make it executable:
```bash
chmod +x start-servers.sh
```

---

**Once satisfied, power off the VM and convert it into a Proxmox template.**

## 🛠️ Troubleshooting & Tweaks

- **Missing Visual C++ Runtime**: Not applicable on Linux.  
- **Disable X11Forwarding** if SSH hangs:  
  ```bash
  sudo sed -i 's/^[# ]*X11Forwarding yes/X11Forwarding no/' /etc/ssh/sshd_config
  sudo systemctl restart sshd
  ```  
- **DLC/Zombies maps**: copy DLC files into `zone/` if needed.  
- **Screen tips**:  
  - List sessions: `screen -ls`  
  - Reattach: `screen -r <session_name>`



---

## 📚 Support & References

- BO3 Guide & files: https://forum.ezz.lol/topic/5/bo3-guide  
- Proxmox Windows 10 best practices: https://pve.proxmox.com/wiki/Windows_10_guest_best_practices
