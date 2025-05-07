# OpenWRT Router Setup Documentation

This repository contains a comprehensive guide for setting up an OpenWRT router in my personal home network, along with configuration steps and troubleshooting tips for various network components. It includes my specific setup, which can serve as a foundation for others to adapt to their own network environments.

The following guides are included in this documentation:

## Overview of the Setup

This setup is designed for users who are looking to configure their own **OpenWRT** router with the following components:

- **Basic OpenWRT Setup**: Initial configuration of OpenWRT, including setting up the router with basic settings like changing passwords, subnetting, and setting DNS.
- **DHCP Static Bindings**: Setting up **static leases** in OpenWRT to assign fixed IP addresses to specific devices, and organizing them with a consistent naming convention.
- **LTE Setup**: Configuration steps for setting up **LTE** connectivity on OpenWRT, specific to my hardware and network environment. This guide provides details on how to make LTE work as your primary or failover internet connection.

## Guides:

- **[Basic OpenWRT Setup](INFRA/Router/OpenWRT/basic-setup.md)**  
  Initial configuration of OpenWRT, including admin password, subnet settings, and DNS changes.

- **[DHCP Static Bindings](INFRA/Router/OpenWRT/dhcp-static-bind.md)**  
  Step-by-step guide on setting up static DHCP leases for devices on your network.

- **[LTE Setup](INFRA/Router/OpenWRT/LTE-setup.md)**  
  Instructions for configuring LTE on OpenWRT, including specific hardware details and troubleshooting.

## Prerequisites

Before starting the setup process, make sure you have:

- A router running **OpenWRT** firmware.
- Basic understanding of networking concepts.
- Your network components ready (e.g., LTE modem, devices for static binding).

## Note

This setup is tailored to my specific network and hardware configuration. It may need adjustments based on the devices you are using. While the steps are written generically to cover broad scenarios, make sure to refer to the OpenWRT documentation or community forums for support on different hardware.

---

### Troubleshooting

If you encounter issues during the setup process, make sure to check the **Troubleshooting** sections in each guide, or visit the OpenWRT forum for community-driven support.

---

### Conclusion

This repository aims to provide a functional setup for OpenWRT, focusing on simplicity and clarity. By following these guides, you'll be able to configure and maintain a stable network environment for your home or small office.

---

### 💡 Further Reading

- [OpenWRT Documentation](https://openwrt.org/docs/)
- [OpenWRT Forum](https://forum.openwrt.org/)
