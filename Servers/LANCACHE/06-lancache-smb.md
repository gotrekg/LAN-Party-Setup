# 06 - Setting Up Samba for LANCache Shared Folders

In this guide, we’ll set up Samba on our Lancache server so users can access the `instalacky` folder and shared files over the network. This allows easy file sharing and access to the game installers.

---

## 🖥️ Install Samba

1. SSH into your Lancache server.
2. Install Samba and utilities:  
   ```bash
   sudo apt update && sudo apt install -y samba samba-common-bin
   ```

---

## 📁 Configure Samba Shares

1. **Edit the Samba configuration file**:  
   ```bash
   sudo nano /etc/samba/smb.conf
   ```

2. **Add the following shares for `instalacky` and `shared`**. Scroll to the bottom and append:

   ```ini
   [instalacky]
   path = /lancache/instalacky
   browseable = yes
   writable = yes
   guest ok = yes
   create mask = 0777
   directory mask = 0777

   [shared]
   path = /lancache/shared
   browseable = yes
   writable = yes
   guest ok = yes
   create mask = 0777
   directory mask = 0777
   ```

   - **`instalacky`**: This share will store all game installation files. It's writable for all users.
   - **`shared`**: This can be a folder where users can share and access other files.

3. **Save and exit** the config file.

---

## 🔑 Create Samba User

You can either allow guest access (as done above) or create a user for Samba access.

1. Create a Samba user (if you don’t want to use guest access):  
   ```bash
   sudo smbpasswd -a masarik
   ```

2. Enable the user:  
   ```bash
   sudo smbpasswd -e masarik
   ```

---

## 🔄 Restart Samba Service

To apply the changes, restart the Samba service:

```bash
sudo systemctl restart smbd
```

---

## 🌐 Access Samba Shares

1. From any device on the LAN (Windows, Linux, etc.), open the file explorer and type in the following address:

   ```
   \<Lancache_Server_IP>\instalacky
   \<Lancache_Server_IP>\shared
   ```

   Replace `<Lancache_Server_IP>` with your actual server’s IP address (e.g., `192.168.40.2`).

2. You should now have access to both the `instalacky` and `shared` folders.

---

## 🛠️ Troubleshooting

- **Access denied or permission issues**: Double-check the folder permissions and Samba config. The folders should be writable and the mask should allow proper access.
- **Not able to see shares**: Try restarting your system or manually mapping the network drive if the share isn’t visible.

---

## 📚 Further Reading

- Samba official documentation: https://www.samba.org/samba/docs/
- Proxmox Storage Options: https://pve.proxmox.com/wiki/Storage

---

**That's it! Now you have a Samba share accessible to all users on the network.**
