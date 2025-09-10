# Multi-NIC Setup

This section covers different options for configuring a Debian host with multiple NICs:

- Onboard 1G NIC (`eno1`) – typically used for management and VM bridge
- Dual 10G NICs (`enp1s0`, `enp1s0d1`) – bonded with **LACP (802.3ad)**

The goal: avoid routing loops or conflicts when mixing management and data traffic.

---

## Option 1: Only use the LACP bond

In this setup, you ignore the onboard NIC. The dual 10G NICs are bonded and provide the single main interface.

**Pros**: Simple, only one interface to manage.  
**Cons**: No separate management plane.

```bash
# Create bond0 with LACP and DHCP
sudo nmcli con add type bond ifname bond0 con-name bond0 mode 802.3ad
sudo nmcli con mod bond0 bond.options "mode=802.3ad,miimon=100,lacp_rate=fast,xmit_hash_policy=layer3+4"
sudo nmcli con mod bond0 ipv4.method auto connection.autoconnect yes

# Add slaves
sudo nmcli con add type ethernet ifname enp1s0   master bond0 con-name bond0-slave0
sudo nmcli con add type ethernet ifname enp1s0d1 master bond0 con-name bond0-slave1
```

---

## Option 2: Recommended – VLAN separation for management

Here, the onboard NIC is connected to a **management VLAN** (example: VLAN 200).  
The 10G bond handles data traffic. A bridge (`br0`) carries the management IP and is usable by VMs.

**Pros**: Proper separation between management and data.  
**Cons**: Requires VLAN support on switch/router.



```bash
# Management bridge
sudo nmcli con add type bridge ifname br0 con-name br0
sudo nmcli con mod br0 ipv4.method manual \
  ipv4.addresses 192.168.200.2/24 \
  ipv4.gateway 192.168.200.1 \
  ipv4.dns 192.168.200.1

# Add eno1 as a bridge-slave
sudo nmcli con add type bridge-slave ifname eno1 master br0 con-name br0-slave0

# Bond for 10G NICs
sudo nmcli con add type bond ifname bond0 con-name bond0 mode 802.3ad
sudo nmcli con mod bond0 bond.options "mode=802.3ad,miimon=100,lacp_rate=fast,xmit_hash_policy=layer3+4"
sudo nmcli con mod bond0 ipv4.method auto ipv4.never-default yes ipv4.route-metric 600

# Bond slaves
sudo nmcli con add type ethernet ifname enp1s0   master bond0 con-name bond0-slave0
sudo nmcli con add type ethernet ifname enp1s0d1 master bond0 con-name bond0-slave1
```

---

## Option 3: Add second subnet

Another approach is to add a **second subnet** to the default LAN bridge on the router.  
The onboard NIC gets a static IP in this subnet, while the 10G bond uses DHCP on the main subnet.

**Pros**: Easier than VLANs, no switch config required.  
**Cons**: Both subnets share the same broadcast domain.


```bash
# Onboard NIC static IP
sudo nmcli con add type ethernet ifname eno1 con-name mgmt \
  ipv4.method manual \
  ipv4.addresses 192.168.200.2/24 \
  ipv4.gateway 192.168.200.1 \
  ipv4.dns 192.168.200.1

# Bond as before
sudo nmcli con add type bond ifname bond0 con-name bond0 mode 802.3ad
sudo nmcli con mod bond0 bond.options "mode=802.3ad,miimon=100,lacp_rate=fast,xmit_hash_policy=layer3+4"
sudo nmcli con mod bond0 ipv4.method auto connection.autoconnect yes

sudo nmcli con add type ethernet ifname enp1s0   master bond0 con-name bond0-slave0
sudo nmcli con add type ethernet ifname enp1s0d1 master bond0 con-name bond0-slave1
```

---

## Verification

After reboot, confirm:

```bash
ip -4 -br a
ip route show
cat /proc/net/bonding/bond0
```

Expected:
- Management (`br0` or `eno1`) has `192.168.200.2`
- `bond0` gets DHCP
- Both slaves active in `/proc/net/bonding/bond0`

---

## Recommendation

- Use **Option 2 (VLAN separation)** for clean setups and when your switch/router supports VLANs.  
- Use Option 1 if you only need a single network.  
- Use Option 3 as a fallback when VLANs aren’t possible.
