# 00 - LANCache Server Specs

Here's the lowdown on the little box doing all the heavy lifting for our LAN party. It's nothing too wild — just solid gear, decent setup, and a bit of Docker magic to make game downloads faster and smoother for everyone.

## 💾 Hardware

- **Model**: Dell OptiPlex 5080 Tower
- **CPU**: Intel Core i7-10700 (8 cores / 16 threads)
- **RAM**: 32GB DDR4 @ 2666MHz
- **Storage**:
  - 512GB NVMe SSD for the OS (boots fast, does its job)
  - PCIe to Dual M.2 NVMe Adapter with 2x 1TB NVMe SSDs for caching all the games and holding install files  
    > 🏴‍☠️ *Yes, including offline/piratey games. Not promoting piracy, just being real — no one's buying full AAA games for one weekend of fragging.*

- **Network**: 
  - 1x Gigabit Ethernet — fallback or for basic LAN needs
  - **10G Double Port Ethernet Card (Intel X540-T2)** — for high-speed LAN performance and ensuring smooth game downloads

## 🖥️ Operating System

Running good ol' Debian 12 (as of 2025-05-07). Super stable, doesn't complain, and I'm comfy with it.

## 🌐 Network Setup

Going into another file later (`04-LACP.md` or whatever I call it). Just a placeholder for now.

## 🧱 Services & Stack

- `lancachenet/monolithic` — the main cache brain
- `lancachenet/sniproxy` — handles HTTPS stuff so modern launchers don’t freak out
- Info page (`info.lan`):
  - Gonna show what’s cached, maybe throw in some stats if I feel fancy
- Captive portal:
  - Hosted on the OpenWRT router
  - No login, no nonsense — just a page that shows up once to say “Hey! Go to info.lan for details.”

> I might do monitoring with Grafana or Netdata one day, but not covering that in this guide — this is fun, not a startup.

## 🧪 Tested Clients

- Windows 10/11 gaming rigs
- Steam
- EA/Origin
- Epic Games
- Blizzard Battle.net
- Riot Games (like LoL and Valorant)


