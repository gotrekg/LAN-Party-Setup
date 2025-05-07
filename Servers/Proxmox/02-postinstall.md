# 02 - Post-Install Tweaks for Proxmox

This is my usual post-install cleanup and setup for Proxmox. It's not required, just something I like to do to keep things tidy and make Proxmox more usable — especially when I'm prepping for things like LAN parties or personal lab use.

## 1. Reclaim space from `local-lvm` (a.k.a. `local-pve`)

First, open a **shell on your Proxmox host** — either via the Web UI (Shell tab) or SSH into the server.

Then run the following commands to remove the `local-lvm` and merge that space into the root volume:

```bash
lvremove /dev/pve/data -y
lvresize -l +100%FREE /dev/pve/root
resize2fs /dev/mapper/pve-root
```

After running that:
- You've freed up the space used by `local-lvm`
- That space is now part of your root filesystem, so you can install more tools, store ISOs, etc., right on the system

> ⚠️ **Heads up**: This wipes the `local-lvm`. If you've already got stuff in there, back it up first.

---

## 2. Clean up in the Proxmox UI

Once you've run those commands, go to the **Proxmox Web UI** and:

- Head to **Datacenter > Storage**
- Remove the `local-lvm` (or `local-pve`) entry — it’ll be broken anyway
- Edit the remaining `local` storage to allow for:
  - `ISO images`
  - `Container templates`
  - `VZDump backup file`
  - Whatever else you want to use it for

Just make sure it has the checkboxes ticked that match your usage — like enabling it for storing ISOs so you can upload them directly through the UI.

---

That's it! This little cleanup just makes Proxmox a bit more straightforward to use in my setups. Feel free to adapt it however you want