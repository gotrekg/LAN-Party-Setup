# Switch Setup Guide

Setting up a switch for your LAN party is usually pretty straightforward, but there are some things to keep in mind. Since each switch can be very different, I’m not providing a step-by-step guide here—this folder is left empty for that reason.

If you’re looking for a basic setup, an **unmanaged switch** is probably your best bet. You just plug things in, and everything works. No fuss.

For larger LAN parties or more complex setups (like using VLANs or managing traffic), the setup will get more complicated. But if you’re at the stage where you need this level of configuration, you're likely not looking for a basic guide anyway.

If you’re connecting servers to your switch, you might want to use **LACP** (Link Aggregation Control Protocol). LACP helps combine multiple links for **more total bandwidth**, but it doesn't increase the maximum speed of a single connection. For example, **LACP 2x1Gb** still limits each connection to 1Gbps—but it allows **more devices to transfer at 1Gbps at the same time**, which can help reduce congestion in high-traffic environments.
