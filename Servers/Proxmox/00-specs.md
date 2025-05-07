# Proxmox Host Specs

Here's what I'm running Proxmox on. Nothing fancy—just a solid little machine I had access to.

## 💻 The Hardware

- **Model**: Dell OptiPlex 7050 Micro
- **CPU**: Intel Core i7-6700T (4 cores / 8 threads)
- **RAM**: 32GB DDR4 @ 3200MHz
- **Storage**: 1TB NVMe SSD
- **Network**: 1x Gigabit Ethernet

## 🔧 Why This Machine?

Simple: it’s what I had on hand.

My work was getting rid of a few of these, so I snagged one. It's compact, quiet, and surprisingly capable. I didn't want to run everything on a giant power-hungry tower, and this thing fits anywhere.

It’s also low-power and runs cool, which makes it great for a LAN party environment—no fans screaming in the background while people are playing.

## 📦 Storage Thoughts

The 1TB NVMe is fast enough for Proxmox itself, game server files, and a few VMs. If I start running out of space, I’ll either clean house or throw on an external USB SSD.

If you're planning something heavier (lots of big games or long-term logs), you’ll probably want to expand storage early.

## 🔌 Networking Notes

It’s just got 1 Gigabit port, which is enough for my needs. If I see it getting saturated, I might add a USB-to-Ethernet adapter (or a USB 3.0 2.5G adapter if the switch supports it), but honestly, for most LAN games, 1G is more than enough.

## 🧠 Final Notes

This isn’t a dream server—it’s just a good, free box that does the job.

I’m not running 24/7 production workloads on it. I’m running LAN game servers, maybe a few containers, and that’s it. For that kind of use, it’s more than enough.
