# Installing Proxmox

This covers the basic Proxmox installation process. Nothing fancy here—just getting the thing installed and ready for the fun stuff.

## 🔽 1. Download & Prepare

- Head over to the [Proxmox Downloads page](https://www.proxmox.com/en/downloads).
- Download the **Proxmox VE ISO Installer**.
- Flash it to a USB stick using something like [Balena Etcher](https://www.balena.io/etcher/) or `dd` if you're old-school.

## 🧭 2. Boot & Install

- Plug the USB into the machine and boot from it.
- Follow the installation steps on the screen.
- I didn’t change any advanced options—just selected the drive and went with the defaults.

## 💡 Hostname Tip

During the install, it will ask you to set a **hostname**. I recommend picking something short and useful—like `proxmox-lanparty` or `proxmox-node1`. You'll need this when setting up static DHCP entries later, so don’t skip naming it properly now.

## ✅ After Install

Once it finishes, the system will reboot and give you a URL to access the Proxmox web interface (something like `https://192.168.1.x:8006`).

From there, everything else can be done through the web UI.

---

That's it. If you're seeing the Proxmox login screen, you’re good to move on.
