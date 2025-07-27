# 06 - Setting Up Samba for LANCache Shared Folders

> **Note:**  
> The name `instalacky` is just the Czech word for "install folder".  
> This share is meant to store all game installation files for easy access by users.

In this guide, we’ll set up Samba on our Lancache server so users can access the `instalacky` folder and shared files over the network. This allows easy file sharing and access to the game installers.

---

## 🖥️ Install Samba

1. SSH into your Lancache server.
2. Install Samba and utilities:  
   ```bash
   sudo apt update && sudo apt install -y samba samba-common-bin
   ```

---

## 🖥️ (Optional) Enable Windows Network Discovery with WSDD

By default, Samba shares may not show up automatically in the "Network" section on Windows 10/11 due to missing WS-Discovery support.  
You can fix this by installing [wsdd](https://github.com/christgau/wsdd), which allows your Samba server to be discoverable by Windows clients.

```bash
sudo apt update
sudo apt install wsdd
sudo systemctl enable --now wsdd
systemctl status wsdd
```

---

## 📂 Create Shared Folders

Before you can share them, make sure the `instalacky` and `shared` folders exist and have the correct permissions:

```bash
sudo mkdir -p /lancache/instalacky
sudo mkdir -p /lancache/shared

# Set permissions and ownership
sudo chmod -R 755 /lancache/instalacky
sudo chown -R root:masarik /lancache/instalacky

sudo chmod -R 777 /lancache/shared
sudo chown -R nobody:nogroup /lancache/shared
```

- `instalacky`: Only `root` and `masarik` can write/paste files; others have read and execute access.
- `shared`: Anyone can read/write.

---

## 📁 Configure Samba Shares

1. **Edit the Samba configuration file**:  
   ```bash
   sudo nano /etc/samba/smb.conf
   ```

2. **Add/modify the following shares for `instalacky` and `shared`**. Scroll to the bottom and append:

   ```ini
   [instalacky]
   path = /lancache/instalacky
   browseable = yes
   writable = yes
   valid users = masarik
   guest ok = no
   create mask = 0755
   directory mask = 0755

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

## 🔑 Create Samba User for Guest-like Access

> **Note:**  
> Windows 11 does not allow guest access to network shares by default.  
> To allow users to access the shares, you need to create a dedicated Samba user for guests.  
> In this example, we'll use the username `LAN` with the password `lan` for guest-like access, and keep `masarik` as your personal user.

1. **Create the `LAN` user as a system user (required for Samba):**
   ```bash
   sudo adduser --no-create-home --disabled-password lan
   ```
   Then add the user to Samba:
   ```bash
   sudo smbpasswd -a lan
   ```
   
   ---

   **Example Samba Configuration (single config for both shares):**

   Append the following to your `/etc/samba/smb.conf`:

   ```ini
   [instalacky]
   path = /lancache/instalacky
   browseable = yes
   writable = yes
   valid users = masarik lan
   guest ok = no
   create mask = 0755
   directory mask = 0755

   [shared]
   path = /lancache/shared
   browseable = yes
   writable = yes
   valid users = masarik lan
   guest ok = no
   create mask = 0777
   directory mask = 0777
   ```

   - `instalacky`: Both `masarik` and `lan` can read/write.
   - `shared`: Both `masarik` and `lan` can read/write.

   > Adjust the `valid users` and permissions as needed for your environment.
2. **Set Samba passwords for your account :**
   ```bash
   
   sudo smbpasswd -a masarik
   ```
   When prompted, enter `lan` as the password for `LAN`, and your chosen password for `masarik`.

3. **Enable both Samba users:**
   ```bash
   sudo smbpasswd -e lan
   sudo smbpasswd -e masarik
   ```



Now, users can access the shares using the username `lan` and password `lan` from their Windows 11 machines, while you can use your `masarik` account for full access.

## 🔄 Restart Samba Service

To apply the changes, restart the Samba service:

```bash
sudo systemctl restart smbd
```

---

## ✅ Finalize Permissions (important!)

If you have issues with access, or after any restore/changes, re-apply the correct permissions:

```bash
sudo chown -R root:masarik /lancache/instalacky
sudo chmod -R 775 /lancache/instalacky
```

---

Now, your `instalacky` share will allow only `root` and `masarik` to write, and others to read/execute.

## 🌐 Access Samba Shares

1. From any device on the LAN (Windows, Linux, etc.), open the file explorer and type in the following address:

   ```
   \\<Lancache_Server_IP>\instalacky
   \\<Lancache_Server_IP>\shared
   ```

   Replace `<Lancache_Server_IP>` with your actual server’s IP address (e.g., `192.168.40.2`).

2. You should now have access to both the `instalacky` and `shared` folders.

---


**That's it! Now you have a Samba share accessible to all users on the network.**
