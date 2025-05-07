# 00 - LANCache Server Specs

Here's the lowdown on the little box doing all the heavy lifting for our LAN party. It's nothing too wild — just solid gear, decent setup, and a bit of Docker magic to make game downloads faster and smoother for everyone.

## 💾 Hardware

- **Model**: Dell OptiPlex 7050 Micro
- **CPU**: Intel Core i7-6700T (4 cores / 8 threads)
- **RAM**: 32GB DDR4 @ 3200MHz (probably overkill, but hey, why not)
- **Storage**:
  - 250GB SSD for the OS (boots fast, does its job)
  - 512GB SSD for sharing random files with folks
  - 1TB NVMe SSD to cache all the games and also hold install files  
    > 🏴‍☠️ *Yes, including offline/piratey games. Not promoting piracy, just being real — no one's buying full AAA games for one weekend of fragging.*

- **Network**: 1x Gigabit Ethernet — it’s fast enough for our LAN needs

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


