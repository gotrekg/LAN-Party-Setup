# 05 - Setting Up LanCache.NET

Now that Docker is installed and working, it's time to bring in LanCache.NET. This will cache game downloads (Steam, Epic, Riot, etc.) to speed things up and save bandwidth for everyone on the LAN.

We're following the official guide from https://lancache.net/docs/.

## 📁 Create a Directory for LanCache

We'll keep things clean and store everything in `/opt/lancache`.

```bash
sudo mkdir -p /opt/lancache
cd /opt/lancache
```

## 🧱 Clone the Docker Compose Setup

```bash
sudo git clone https://github.com/lancachenet/docker-compose.git .
```

## 📝 Set Up Environment Variables

Edit the `.env` file provided in the repo:

```bash
sudo nano .env
```

### Key variables to update:

- `LANCACHE_IP`: the IP of this machine (e.g. `192.168.40.2`)
- `CACHE_ROOT`: where cached files go (e.g. `/lancache/data`)
- `CACHE_DISK_SIZE`: how much space to allow for caching  
  > Set this based on the size of your ZFS pool or disk. For example, if your drive is 1TB, setting `900g` is safe.
- `UPSTREAM_DNS`: This will be your OpenWRT router's IP (e.g. `192.168.1.1`)  
  > This is needed so you can resolve internal names like `pve.lan`

Save and close the file when done.

## ▶️ Bring Up LanCache

Start it up with Docker Compose:

```bash
sudo docker compose up -d
```

This will start:

- `lancache-dns` – handles DNS redirection
- `lancache` – the actual caching proxy
- `sniproxy` – handles HTTPS traffic

## ✅ Check That It's Running

You can verify it's running with:

```bash
sudo docker ps
```

You should see the three containers up and running.

## 🌐 Configure DHCP DNS in OpenWRT

To ensure all LAN devices use the cache, the OpenWRT router is configured to push the LanCache server IP as DNS via DHCP.

For the exact steps, see:  
[`INFRA/Routers/OpenWRT/04-dhcp-dns-change.md`](../../INFRA/Routers/OpenWRT/04-dhcp-dns-change.md)

This is required to make the cache work seamlessly during the LAN event.

---

At this point, LanCache is running, properly integrated with the network, and ready to start caching.
