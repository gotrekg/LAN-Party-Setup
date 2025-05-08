# 04 - Changing DHCP DNS in OpenWRT

This quick guide shows how to configure your OpenWRT router’s DHCP server so that all LAN clients use your LanCache server for DNS.

> **Note:** Replace `192.168.1.2` below with the IP address of your LanCache host.

## 🕸️ Via LuCI (Web Interface)

1. Log in to your OpenWRT web UI (e.g. `http://192.168.1.1`).
2. Navigate to **Network** > **Interfaces**.
3. Click **Edit** on the **LAN** interface.
4. Go to the **DHCP Server** tab.
5. Click **Advanced Settings**.
6. In the **DHCP-Options** field, add:
   ```
   6,192.168.1.2
   ```
7. Click **Save & Apply**.

This tells DHCP to hand out `192.168.1.2` as the DNS server to all clients.

## 🖥️ Via SSH (UCI Commands)

If you prefer the command line, SSH into your OpenWRT router and run:

```bash
# Append your LanCache IP to DHCP options
uci add_list dhcp.lan.dhcp_option="6,192.168.222.229"

# Commit and restart dnsmasq
uci commit dhcp
/etc/init.d/dnsmasq restart
```

## ✅ Verify

- On a client machine, check its DNS setting (e.g. `ipconfig /all` on Windows or `cat /etc/resolv.conf` on Linux).
- You should see the LanCache IP (`192.168.2.1`) as the DNS server.
