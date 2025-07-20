# Prerequisites

## Required Knowledge
- Basic understanding of networking concepts
- Familiarity with SSH and command-line operations
- Understanding of VLANs and multicast protocols

## Hardware Requirements
- **Router:** GL.iNet Flint 3 (or compatible OpenWrt device)
- **Cabling:** Ethernet cable from WAN port to KPN ONT (fiber modem)
- **Access:** Computer with SSH client and web browser

## Network Credentials
- **PPPoE credentials:** Username and password for KPN internet (usually both are "internet")
- **Wi-Fi credentials:** SSID and password of your choice

## Physical Port Configuration
```
Port 0 = WAN (connected to KPN ONT)
Port 1 = LAN (general network devices + KPN TV+ Box)
Port 2 = LAN (general network devices + KPN TV+ Box)
Port 3 = LAN (general network devices + KPN TV+ Box)
Port 4 = LAN (general network devices + KPN TV+ Box)
```

## Tips

### Backup Configuration Files
**Always create backups before editing configuration files!** This prevents data loss and allows easy recovery if something goes wrong.
**Reboot if the router hangs** When connectivity issues, reboot the system the soft or the hard way.

```bash
# Example backup commands
cp /etc/config/network /etc/config/network.bak
cp /etc/config/igmpproxy /etc/config/igmpproxy.bak
cp /etc/config/firewall /etc/config/firewall.bak
cp /etc/config/dhcp /etc/config/dhcp.bak
```

### General Best Practices
- Test each configuration step before proceeding to the next
- Keep notes of any custom settings you modify
- Use SSH for advanced configurations when the web interface is insufficient
- Monitor system logs for any errors during configuration 