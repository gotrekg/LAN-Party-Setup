# 02 - Post-Install for Debian LANCache Box

Once Debian is installed and you're in the system, here's what I usually do. You can go way deeper with security and tweaks if you want — like setting up SSH keys, disabling root login, etc. I’m not doing all that here.

> **Note:**  
> For this step, I am using the onboard 1G NIC for setup and networking. LACP (for the 10G NIC) will be covered in a separate guide.

## 🔧 My Quick Setup

- Change the root/user password to something solid  
  *# Make sure to choose a strong password — don’t use “password123”!*
- Set up **passwordless sudo** (so I don’t have to type the password all the time)  
  *# Editing sudoers can break sudo if done incorrectly. Always use `sudo visudo` to prevent syntax errors.*
- Run a full update & upgrade  
  ```bash
  sudo apt update && sudo apt upgrade -y
  ```
  *# Watch out for any errors — if packages fail to install, fix those before moving on.*

---

## Network Manager & Bridge Setup

I switched from the classic `/etc/network/interfaces` setup to using **NetworkManager** for managing network interfaces. This makes it easier if you want to use GUI tools or `nmcli` later.  
Since I’ll be installing Cockpit and creating VMs, I’ll also set up a bridge interface for VM networking.

### Steps:

1. **Install NetworkManager**
   ```bash
   sudo apt-get install network-manager
   ```

2. **Back up the old interfaces file in case you need it**
   ```bash
   sudo cp /etc/network/interfaces /etc/network/interfaces.bak
   ```

3. **Edit the interfaces file and leave only loopback**
   ```bash
   sudo nano /etc/network/interfaces
   ```
   Leave only:
   ```
   auto lo
   iface lo inet loopback
   ```

4. **Tell NetworkManager to manage interfaces previously handled by ifupdown**
   ```bash
   sudo nano /etc/NetworkManager/NetworkManager.conf
   ```
   Change:
   ```
   [ifupdown]
   managed=false
   ```
   to:
   ```
   [ifupdown]
   managed=true
   ```

5. **Restart NetworkManager**
   ```bash
   sudo systemctl restart NetworkManager
   ```

6. **Create a bridge for VM networking**  
   > **Note:**  
   > Replace `<your-nic-name>` with your actual onboard NIC name.  
   > Find your NIC name using `ip a` or `nmcli device status`.  
   > Common names include `eth0`, `ens33`, `enp3s0`, etc.

   ```bash
   sudo nmcli connection add type bridge autoconnect yes con-name br0 ifname br0
   sudo nmcli connection add type bridge-slave autoconnect yes con-name br0-port1 ifname <your-nic-name> master br0
   sudo nmcli connection modify br0 ipv4.method auto
   sudo nmcli connection up br0
   ```

> **⚠️ IMPORTANT:**  
> The bridge setup will only fully apply after you reboot.  
> Your host's IP address may change after rebooting, so check your new IP with `ip a` or `nmcli device status`.  
> If you are connected via SSH, you may lose your connection and need to reconnect using the new IP.

7. **Reboot to apply bridge changes and get your new IP**
   ```bash
   sudo reboot
   ```

8. **Verify bridge and network status (after reboot)**
   ```bash
   nmcli device status
   ```

9. **Disable the old networking service to avoid conflicts**
   ```bash
   sudo systemctl stop networking
   sudo systemctl disable networking
   ```

---

*# Note: If your network goes down and you lose SSH, you can revert by restoring the backup file:*
```bash
sudo cp /etc/network/interfaces.bak /etc/network/interfaces
sudo systemctl enable networking
sudo service networking start
sudo service NetworkManager stop
```

---

## Cockpit & Virtual Machine Manager

Cockpit is a web-based admin interface for servers. You can use it to manage your system and virtual machines.

**Why Cockpit and Virtual Machines?**  
This server will act as both the LANCache and a central info page for users.  
I want the info page to be hosted on a separate VM, so users can simply visit `info.lan` instead of having to remember a port number like `lancache.lan:8080`.  
This makes it much easier for everyone at the LAN party to access important info, rules, and downloads.

> **Tip:**  
> For the most up-to-date installation instructions, visit the official Cockpit website:  
> [https://cockpit-project.org/running.html#debian](https://cockpit-project.org/running.html#debian)

As of now, the installation process on Debian is:

```bash
. /etc/os-release
echo "deb http://deb.debian.org/debian ${VERSION_CODENAME}-backports main" | sudo tee /etc/apt/sources.list.d/backports.list
sudo apt update
sudo apt install -t ${VERSION_CODENAME}-backports cockpit
```

- Cockpit will be enabled by default after installation.
- To manage virtual machines, also install:
  ```bash
  sudo apt install cockpit-machines
  ```
- Access Cockpit in your browser at:  
  `https://<your-server-ip>:9090`

Log in with your system user credentials.
