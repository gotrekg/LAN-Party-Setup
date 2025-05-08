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

#### Example `.env` values (adjust as needed):

```
USE_GENERIC_CACHE=true        # Use monolithic generic cache
LANCACHE_IP=192.168.222.229   # This is the IP of the server itself
DNS_BIND_IP=192.168.222.229   # DNS service bind IP on the host
UPSTREAM_DNS=192.168.222.1     # OpenWRT router IP for upstream DNS
CACHE_ROOT=/lancache/data      # Path to ZFS cache dataset
CACHE_DISK_SIZE=900g           # Max cache size (based on ZFS pool)
MIN_FREE_DISK=10g              # Minimum free space to maintain
CACHE_INDEX_SIZE=250m          # Index memory size (250m per TB recommended)
CACHE_MAX_AGE=3650d            # Max age of cached content in days
TZ=Europe/Prague               # Timezone for container logs
```


- `LANCACHE_IP`: the IP of this machine (e.g. `192.168.1.2`)
- `CACHE_ROOT`: where cached files go (e.g. `/lancache/data`)
- `CACHE_DISK_SIZE`: how much space to allow for caching  
  > Set this based on the size of your ZFS pool or disk. For example, if your drive is 1TB, setting `900g` is safe.
- `UPSTREAM_DNS`: This should be your OpenWRT router's IP (e.g. `192.168.1.1`)  
- `DNS_BIND_IP`: the IP on the host where the DNS server should bind (e.g. `192.168.1.2`)  
  > Set this if your machine has multiple interfaces—defaults to all interfaces if left blank.
  > This is needed so you can resolve internal names like `pve.lan`


Save and close the file when done.

## ✅ Check That It's Running

Start it up with Docker Compose:

```bash
sudo docker compose up -d
```

Then confirm it's running with:

```bash
sudo docker ps
```

You should see `lancache`, `lancache-dns`, and `sniproxy` up and running.

## 🌐 Configure DHCP DNS in OpenWRT

To ensure all LAN devices use the cache, the OpenWRT router is configured to push the LanCache server IP as DNS via DHCP.

For the exact steps, see:  
[`INFRA/Routers/OpenWRT/04-dhcp-dns-change.md`](../../INFRA/Routers/OpenWRT/04-dhcp-dns-change.md)

---

## 🔍 DNS Check on Host (Optional but Useful)

Make sure your server itself uses the correct upstream DNS (i.e. your OpenWRT router) and not itself.

### Check current DNS:

```bash
cat /etc/resolv.conf
```

If you see something like:

```
nameserver 192.168.1.1
```

Then you're good — that’s your OpenWRT router.

