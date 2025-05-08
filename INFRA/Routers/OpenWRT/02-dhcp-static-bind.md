# DHCP Static Bindings in OpenWRT

Configuring **DHCP Static Bindings** allows you to assign fixed IP addresses to specific devices in your network. This is particularly useful for devices like printers, servers, or other equipment that require a static IP. The important setting here is the **Instance** (typically `.lan`), which makes managing these leases much easier by using names rather than IP addresses.

## Steps to Set Up DHCP Static Bindings

1. **Access the OpenWRT Interface**
   - Open a web browser and go to your OpenWRT router’s IP address (e.g., `http://192.168.1.1`).
   - Log in with your router credentials (usually `root` for the username).

2. **Navigate to Static Leases Settings**
   - Go to **Network** > **DHCP and DNS**.
   - Under the **Static Leases** section, you'll configure the static IP bindings for your devices.

3. **Add a Static Lease**
   - In the **Static Leases** section, click **Add** to create a new static lease.
   - **MAC Address**: Enter the MAC address of the device you want to assign a static IP to. This can usually be found in the device's network settings.
   - **IP Address**: Enter the desired IP address (within the range of your local network).
   - **Hostname**: Give the device a friendly name (e.g., `printer.lan`, `server.lan`), which will make managing the device easier later on.

4. **Set the Instance (Optional but Recommended)**
   - The **Instance** field should be set to `.lan` (e.g., `printer.lan`). This is important for organizing and managing devices in your network by their hostnames rather than IP addresses.
   - Using a consistent naming convention (e.g., `device-name.lan`) will help you easily identify devices by name, making future configuration or troubleshooting easier.

5. **Save and Apply**
   - After filling in the required fields, click **Save & Apply** to save your static lease configuration.
   - Your devices will now always be assigned the same IP address based on their MAC address.

---

### Notes:
- **Why Use Static Leases?** Static leases are particularly useful for network devices that need a fixed IP address, such as servers, printers, or any networked devices that need to be consistently accessed via the same IP.
- The **Instance (.lan)** setting helps with **easier identification** and **management** by creating a simple naming convention (e.g., `printer.lan`, `server.lan`).
- Be sure to avoid assigning IPs to devices that fall outside the DHCP pool to avoid conflicts.
  
---

### Troubleshooting:
- If your static leases aren’t working, check that the MAC address and IP address are entered correctly.
- Ensure that the **Instance** field is correctly set to `.lan` or your preferred local domain to avoid any naming issues.

---

### Conclusion:
By setting up **static DHCP leases** and using the **Instance (.lan)** naming convention, you’ll have better control over your network, as each device will have a consistent, easy-to-remember name rather than just an IP address. This simplifies future management and troubleshooting.

---

### 💡 Additional Tip:
You can view the list of assigned static leases and their status by navigating to **Network** > **DHCP and DNS**, under the **Static Leases** section, where all your configured leases will be displayed.

---

### Notes:
- This guide assumes you're using the default OpenWRT configuration and are familiar with basic network setup. For more complex configurations or additional features, refer to OpenWRT’s [documentation on DHCP](https://openwrt.org/docs/).

---

### 🧑‍💻 Explanation:
- **Instance (.lan)**: It's highly recommended to use a **.lan** suffix for internal devices, so it's easier to manage them by name. This is crucial for users who might not be familiar with networking conventions but want an organized system.
- **Adding static leases**: Simple and straightforward, with a friendly device name assigned for easier identification.
- **Apply and test**: After setting up static leases, ensure that the devices in question correctly get their assigned IPs and are easy to manage by name.


