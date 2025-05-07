# LAN Party Server Setup

This section covers the server-side setup used for LAN parties, split between two dedicated machines:

- **Proxmox**: Hosts game servers and other virtualized services.
- **LANCACHE**: Acts as a LAN cache server to reduce bandwidth usage and speed up game downloads/updates.

## Why Two Machines?

You might wonder why I decided to separate these roles instead of just running everything on one big server. Here’s why:

- **Isolation**: Keeping the caching system separate from game servers reduces interference, simplifies troubleshooting, and ensures one service doesn't choke out the other.
- **Flexibility**: If something breaks (especially under LAN party pressure), I can reboot or reconfigure one machine without affecting the other.
- **Cleaner Setup**: Network-wise and logically, it’s easier to manage. Each machine has one job and does it well.

## Folder Structure

- `Proxmox/` – Docs and steps related to setting up and configuring the Proxmox host and its VMs/containers.
- `LANCACHE/` – Setup steps for the LANCache server, including configuration, DNS changes, and storage considerations.

Specs and detailed config for each server are found inside their respective folders.

---

If you're setting up something similar, feel free to adapt this structure—but keep in mind this documentation reflects **my actual setup**, not a generic guide.
