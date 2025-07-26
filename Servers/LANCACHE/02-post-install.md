# 02 - Post-Install for Debian LANCache Box

Once Debian is installed and you're in the system, here's what I usually do. You can go way deeper with security and tweaks if you want — like setting up SSH keys, disabling root login, etc. I’m not doing all that here.

## 🔧 My Quick Setup

- Change the root/user password to something solid  
  *# Make sure to choose a strong password — don’t use “password123”!*
- Set up **passwordless sudo** (so I don’t have to type the password all the time)  
  *# Editing sudoers can break sudo if done incorrectly. Always use `sudo visudo` to prevent syntax errors.*
- Run a full update & upgrade  
  ```bash
  apt update && apt upgrade -y
  ```
  *# Watch out for any errors — if packages fail to install, fix those before moving on.*

---

## Network Manager Setup

I switched from the classic `/etc/network/interfaces` setup to using **NetworkManager** for managing network interfaces. This makes it easier if you want to use GUI tools or `nmcli` later.

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

5. **Restart NetworkManager to apply changes**
   ```bash
   sudo service NetworkManager restart
   ```

6. **Verify that NetworkManager is running and managing devices**
   ```bash
   nmcli device status
   ```

7. **Disable the old networking service to avoid conflicts**
   ```bash
   sudo systemctl stop networking
   sudo systemctl disable networking
   ```

*# Note for newcomers: If at any point your network goes down and you lose SSH, you can revert by restoring the backup file:*
```bash
sudo cp /etc/network/interfaces.bak /etc/network/interfaces
sudo systemctl enable networking
sudo service networking start
sudo service NetworkManager stop
```
