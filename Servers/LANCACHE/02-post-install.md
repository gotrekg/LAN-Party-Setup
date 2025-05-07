# 01 - Post-Install for Debian LANCache Box

Once Debian is installed and you're in the system, here's what I usually do. You can go way deeper with security and tweaks if you want — like setting up SSH keys, disabling root login, etc. I’m not doing all that here.

## 🔧 My Quick Setup

- Change the root/user password to something solid
- Set up **passwordless sudo** (so I don’t have to type the password all the time)
- Run a full update & upgrade

```bash
apt update && apt upgrade -y
```

That’s it. After this, the box is ready for the actual LANCache setup.
