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

## Known Issues

### WLAN Issues After Firmware Upgrade
After upgrading to a new version of the firmware, WLAN may stop working with the following symptoms:
- Wireless networks show "not associated" status
- Networks display "Checking for channel availability. This process will take 60 Seconds. Please wait……"
- Guest networks and MLO (Multi-Link Operation) may also be affected

**Workaround:** Try rebooting the router or resetting the the whole configuration if the issue persists.

### WiFi Channels Not Showing Despite Being Enabled
WiFi channels may suddenly stop appearing even though they are enabled in the GUI interface.

**Workaround:** Disable 802.11r Fast Transition in the wireless settings to resolve this issue.

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