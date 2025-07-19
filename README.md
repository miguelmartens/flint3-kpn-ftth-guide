# Flint 3 OpenWrt Router Setup Guide for KPN FTTH

## Introduction

This guide provides a complete setup for the GL.iNet Flint 3 router with OpenWrt to work with KPN FTTH internet and IPTV services. The Flint 3 is a compact, powerful router that offers excellent performance and flexibility for advanced networking configurations.

This setup will configure:
- **VLAN 6** for PPPoE Internet connection
- **VLAN 4** for KPN IPTV (multicast) services
- **Port-based VLAN configuration** for optimal performance
- **IGMP proxy** for multicast TV streaming
- **AdGuard Home** for DNS filtering and security

The configuration uses the default IP range of `192.168.8.x` and provides both wired and wireless IPTV options.

---

## Prerequisites

### Required Knowledge
- Basic understanding of networking concepts
- Familiarity with SSH and command-line operations
- Understanding of VLANs and multicast protocols

### Hardware Requirements
- **Router:** GL.iNet Flint 3 (or compatible OpenWrt device)
- **Cabling:** Ethernet cable from WAN port to KPN ONT (fiber modem)
- **Access:** Computer with SSH client and web browser

### Network Credentials
- **PPPoE credentials:** Username and password for KPN internet (usually both are "internet")
- **Wi-Fi credentials:** SSID and password of your choice

### Physical Port Configuration
```
Port 0 = WAN (connected to KPN ONT)
Port 1 = LAN (general network devices + KPN TV+ Box)
Port 2 = LAN (general network devices + KPN TV+ Box)
Port 3 = LAN (general network devices + KPN TV+ Box)
Port 4 = LAN (general network devices + KPN TV+ Box)
```

---

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

---

## Part 1: Initial Router Setup

### 1.1 First-Time Hardware Setup

1. **Connect Hardware:**
   - Connect the WAN port (Port 0) to your KPN ONT using an Ethernet cable
   - Power on the Flint 3 router
   - Wait for the router to fully boot (LED indicators will stabilize)

2. **Initial Wi-Fi Setup:**
   - Scan the QR code on the bottom of the router or manually connect to the default Wi-Fi network
   - Follow the setup wizard to configure:
     - Admin password for the router
     - Wi-Fi SSID and password
     - Basic network settings

3. **Access Router Interface:**
   - Connect your computer to the router via Wi-Fi or LAN cable
   - Open a web browser and navigate to `192.168.8.1`
   - Login with the admin credentials you set during the wizard

### 1.2 Access LuCI Advanced Interface

1. **Navigate to LuCI:**
   - In the GL.iNet web interface, go to **SYSTEM** → **Advanced Settings**
   - Click **"Go To LuCI"** to access the full OpenWrt configuration interface
   - Login with the same admin password you set during the initial setup

2. **Important Notice - Switch Topology Warning:**
   > **⚠️ Warning:** You may see a message: "Switch switch0 has an unknown topology - the VLAN settings might not be accurate."
   >
   > This is a known issue with some OpenWrt builds and can be safely ignored for now. The switch0 configuration will be handled manually in the network configuration file.

### 1.3 SSH Access Setup

1. **Enable SSH Access:**
   - In LuCI, go to **System** → **Administration** → **SSH Access**
   - Ensure SSH is enabled and configured for root access

2. **Connect via SSH:**
   ```bash
   ssh root@192.168.8.1
   ```
   - Use the same admin password you set during the initial setup

---

## Part 2: Core Network Configuration

### 2.1 Backup and Prepare Network Configuration

1. **Navigate to Config Directory:**
   ```bash
   cd /etc/config/
   ```

2. **Save Current ULA Prefix:**
   ```bash
   cat network | grep -i "option ula_prefix"
   ```
   - Note the ULA prefix value (e.g., `fd44:9f52:3f1f::/48`)
   - **Save this value** - you'll need it for the new configuration

3. **Create Backup:**
   ```bash
   cp network network.bak
   ```

4. **Remove Original and Create New:**
   ```bash
   rm network
   vim network
   ```

### 2.2 Apply New Network Configuration

1. **Copy the Complete Configuration:**
   Replace the entire content with the configuration from your repository, making sure to:
   - Replace the `ula_prefix` value with the one you saved earlier
   - Verify all VLAN configurations match your port setup

2. **Save:**
   - Save the file (`:wq` in vim)

3. **Apply Network Changes:**
   ```bash
   uci commit network
   /etc/init.d/network reload
   ```
   - `reload` applies the new settings without completely rebooting the router
   - If you notice that a certain interface doesn't come up properly, you can also do a full restart:

     ```bash
     /etc/init.d/network restart
     ```

4. **Optional: Check the status**
   Check if your interfaces are up and have the correct IPs:

   ```bash
   ifstatus wan
   ifstatus lan
   ifstatus iptv
   ```

After these steps, OpenWrt uses your new network configuration immediately.

### 2.3 Verify Internet Connection

1. **Check Connection Status:**
   - Return to the GL.iNet web interface
   - Navigate to **INTERNET** section
   - Verify that you have an active internet connection

2. **Test Internet Access:**
   - Open a web browser and test basic internet connectivity
   - Run speed tests to verify performance
   - Check that both IPv4 and IPv6 are working properly

### 2.4 Configure Additional Settings

1. **Time Zone Configuration:**
   - In the GL.iNet web interface
   - Set your correct timezone
   - Save and apply changes

2. **IGMP Snooping Settings:**
   - In the GL.iNet web interface
   - Verify IGMP snooping is enabled on the LAN bridge
   - Ensure IGMP version 3 is being used for optimal multicast performance

3. **IPv6:**
   - In the GL.iNet web interface
   - Enable IPv6 and set it to 'NAT6' Mode and DNS acquisition method to 'Automatic'

---

## Part 3: Advanced Configuration Sections

### 3.1 IGMP Proxy and Snooping

**IGMP (Internet Group Management Protocol)** is essential for multicast traffic handling:

- **IGMP Snooping:** Allows the switch to monitor IGMP traffic and forward multicast packets only to ports that have requested them
- **IGMP Proxy:** Forwards IGMP messages between different network segments (essential for IPTV)

**Configuration Details:**
- IGMP snooping is enabled on the LAN bridge (`br-lan`)
- IGMP version 3 is enforced for better efficiency
- The proxy configuration handles upstream (IPTV VLAN) and downstream (LAN) traffic

### 3.2 IPv6 Configuration

**IPv6 settings are automatically configured:**
- ULA (Unique Local Address) prefix is preserved from your original configuration
- DHCPv6 and RA (Router Advertisement) are enabled for automatic IPv6 address assignment
- IPv6 forwarding is properly configured for all interfaces

### 3.3 MTU Settings

**MTU (Maximum Transmission Unit) optimization (Baby jumbo frames):**
- WAN interface: 1500 bytes (standard for PPPoE)
- VLAN interfaces: 1508 bytes (accounting for VLAN tag overhead)
- Physical interfaces: 1512 bytes (accounting for additional overhead)

These settings ensure optimal performance while avoiding packet fragmentation.

### 3.4 VLAN Configuration Explanation

**VLAN Structure:**
- **VLAN 1:** Default LAN traffic (Ports 1, 2, 3)
- **VLAN 4:** IPTV traffic (Port 4 + CPU)
- **VLAN 6:** Internet traffic (WAN port + CPU)

**Port Tagging:**
- CPU ports are tagged (`t`) to handle multiple VLANs
- LAN ports are untagged for simple device connectivity
- WAN port is tagged for VLAN 6 (Internet)

---

## Part 4: AdGuard Home Setup

### 4.1 Install and Enable AdGuard Home

1. **Navigate to Applications:**
   - In the GL.iNet web interface, go to **APPLICATIONS**
   - Find **AdGuard Home** and click on it

2. **Enable AdGuard Home:**
   - Toggle the switch to enable AdGuard Home
   - Wait for the service to start

3. **Optional: Enable Client Request Handling:**
   - Enable "AdGuard Home Handle Client Requests" for enhanced functionality
   - This provides better client identification and filtering

### 4.2 Access AdGuard Home Interface

1. **Open AdGuard Home:**
   - Click on the AdGuard Home interface link
   - This will open the full AdGuard Home web interface

2. **Initial Setup:**
   - Follow the AdGuard Home setup wizard
   - Configure admin credentials
   - Set up basic filtering rules and configure DNS
   - Make sure Parallel requests is selected
   - Enable DNSSEC

### 4.3 Important DNS Configuration Notice

> **⚠️ Important:** When AdGuard Home is enabled, the router's built-in DNS settings become inactive. All DNS configuration must be done through the AdGuard Home interface.

**What this means:**
- DNS server settings in the router's web interface will not work
- All DNS queries are redirected to AdGuard Home (port 3053)
- DNS filtering, blocking, and logging are handled by AdGuard Home

**Benefits:**
- Advanced ad blocking and tracking protection
- Detailed DNS query logging
- Custom filtering rules and whitelists
- Better privacy and security

---

## Part 5: IPTV Plugin Installation

### 5.1 Install IGMP Proxy Plugin

Before proceeding with IPTV configuration, you need to install the IGMP proxy plugin:

1. **Navigate to Plugin Management:**
   - In the GL.iNet web interface, go to **APPLICATIONS**
   - Click on **Plug-ins**

2. **Search and Install:**
   - Search for `igmpproxy`
   - Click **Install** to add the IGMP proxy functionality

3. **Verify Installation:**
   - After installation, verify that the plugin appears in your installed plugins list
   - The IGMP proxy service should be available for configuration

---

## Part 6: IPTV Configuration

### 6.1 Get IPTV Gateway Information

**Note:** All IPTV configuration steps require SSH access to the router. Make sure you have SSH enabled and can connect to the router.

1. **Connect to Router via SSH:**
   ```bash
   ssh root@192.168.8.1
   ```
   - Use the same admin password you set during the initial setup

2. **Check Kernel IP Routing Table:**
   ```bash
   route
   ```
   - Look for entries with `Iface` column showing `eth0.4`
   - Note the `Gateway` IP address for the IPTV interface
   - **Save this Gateway IP address** - you'll need it for the next step

3. **Update Network Configuration:**
   ```bash
   vim /etc/config/network
   ```
   - Find the section: `config route` with `option interface 'iptv'`
   - Replace the `option gateway 'x.x.x.x'` value with the Gateway IP you saved
   - Save the file (`:wq` in vim)

### 6.2 Apply Network Changes

**In the SSH terminal:**

1. **Commit and Reload Network:**
   ```bash
   uci commit network
   /etc/init.d/network reload
   ```

### 6.3 Configure Firewall

**In the SSH terminal:**

1. **Navigate to Config Directory:**
   ```bash
   cd /etc/config/
   ```

2. **Backup and Replace Firewall Configuration:**
   ```bash
   cp firewall firewall.bak
   rm firewall
   vim firewall
   ```

3. **Add Firewall Configuration:**
   - Copy the complete firewall configuration from your repository
   - Save the file (`:wq` in vim)

4. **Apply Firewall Changes:**
   ```bash
   uci commit firewall
   /etc/init.d/firewall reload
   ```
   - `reload` rebuilds your iptables rules without completely stopping the daemon
   - If you notice something goes wrong or want to start completely "clean", you can also use:

     ```bash
     /etc/init.d/firewall restart
     ```

5. **Optional: Check your rules**
   You can check if your new zones/forwarding/rules are included with:

   ```bash
   iptables -L -v
   iptables -t nat -L -v
   ```

After these steps, your new firewall zones, forwarding rules and custom rules are active.

### 6.4 Configure DHCP

**In the SSH terminal:**

1. **Backup and Replace DHCP Configuration:**
   ```bash
   cp dhcp dhcp.bak
   rm dhcp
   vim dhcp
   ```

2. **Add DHCP Configuration:**
   - Copy the complete DHCP configuration from your repository
   - **Important:** Replace the broadcast address in the IPTV DHCP options:
     - Find `list dhcp_option '28,192.168.8.255'`
     - Ensure this matches your LAN broadcast address (in my case `192.168.8.255`)
   - Save the file (`:wq` in vim)

3. **Apply DHCP Changes:**
   ```bash
   uci commit dhcp
   /etc/init.d/dnsmasq reload
   ```

   * `reload` only rebuilds the config without completely stopping the daemon.
   * If you see problems you can also do a full restart:

     ```sh
     /etc/init.d/dnsmasq restart
     ```

4. **Optional: check if options are active**

   * View the generated dnsmasq config:

     ```sh
     cat /var/etc/dnsmasq.*.conf | grep dhcp-option
     ```
   * Read the log for DHCP leases/join events:

     ```sh
     logread | grep dnsmasq
     ```

### 6.5 Configure IGMP Proxy

**In the SSH terminal:**

1. **Backup and Replace IGMP Proxy Configuration:**
   ```bash
   cp igmpproxy igmpproxy.bak
   rm igmpproxy
   vim igmpproxy
   ```

2. **Add IGMP Proxy Configuration:**
   - Copy the complete IGMP proxy configuration from your repository
   - **Important:** Verify the LAN address range in the upstream configuration:
     - Find `list altnet '192.168.8.0/24'` in the upstream section
     - Ensure this matches your LAN subnet (in my case `192.168.8.0/24`)
   - Save the file (`:wq` in vim)

3. **Apply IGMP Proxy Changes:**
   ```bash
   uci commit igmpproxy
   /etc/init.d/igmpproxy restart
   ```

4. **Optional: check if it's working**
   ```bash
   ps w | grep igmpproxy
   logread | grep igmpproxy
   ```

### 6.6 Verify Configuration in LuCI

**Switch back to the web browser:**

1. **Open LuCI Web Interface:**
   - In the GL.iNet web interface, go to **SYSTEM** → **Advanced Settings**
   - Click **"Go To LuCI"**
   - Login with your admin password

2. **Check Network Configuration:**
   - Navigate to **Network** → **Interfaces**
   - Verify that the `iptv` interface is present and configured
   - Check that the IPTV interface has obtained an IP address

3. **Check Firewall Rules:**
   - Navigate to **Network** → **Firewall**
   - Verify that the `iptv` zone is present
   - Check that forwarding rules between `lan` and `iptv` are configured

4. **Check DHCP Configuration:**
   - Navigate to **Network** → **DHCP and DNS**
   - Verify that IPTV DHCP options are present
   - Check that the broadcast address is correctly set

### 6.7 Final Router Reboot

**Back in the SSH terminal:**

1. **Sanity Check and Reboot:**
   ```bash
   reboot
   ```
   - This ensures all configurations are properly loaded
   - After reboot, verify that all services are running correctly

**After Reboot:**
- Wait for the router to fully boot (2-3 minutes)
- Reconnect to the router via SSH or web interface
- Verify that all services are running properly

---

## Part 7: IPTV Testing and Verification

### 7.1 Test IPTV Connectivity

1. **Connect KPN TV+ Box:**
   - Connect your KPN TV+ Box to a LAN port
   - Power on the TV+ Box and wait for it to boot

2. **Verify IP Address Assignment:**
   - The TV+ Box should receive an IP address from the `192.168.8.x` range
   - Check in LuCI under **Network** → **DHCP and DNS** → **Static Leases** to see the device

3. **Test Live TV:**
   - Navigate through the TV+ Box menu
   - Try changing channels - should be instant and smooth
   - Verify that both SD and HD channels work properly

### 7.2 Troubleshooting

**If IPTV is not working:**

1. **Check IGMP Proxy Status:**
   ```bash
   ps w | grep igmpproxy
   logread | grep igmpproxy
   ```

2. **Verify VLAN Configuration:**
   ```bash
   ifstatus iptv
   ```

3. **Check Firewall Rules:**
   ```bash
   iptables -L -v | grep iptv
   ```

4. **Monitor Multicast Traffic:**
   ```bash
   tcpdump -i eth0.4 -n multicast
   ```

---

## Configuration Files

This repository includes the complete configuration files needed for the KPN FTTH setup. All files are located in the `config/` directory:

### Available Configuration Files

- **`config/network`** - Complete network configuration including:
  - VLAN setup (VLAN 1 for LAN, VLAN 4 for IPTV, VLAN 6 for WAN)
  - Interface configurations for WAN, LAN, and IPTV
  - PPPoE settings for KPN internet connection
  - IPv6 configuration with ULA prefix

- **`config/firewall`** - Firewall rules and zones including:
  - Zone definitions for LAN, WAN, and IPTV
  - Forwarding rules between zones
  - Custom rules for multicast traffic
  - NAT configuration for internet access

- **`config/dhcp`** - DHCP and DNS configuration including:
  - DHCP server settings for LAN and IPTV interfaces
  - Static lease configurations
  - DNS settings and options
  - IPTV-specific DHCP options for KPN TV+ Box

- **`config/igmpproxy`** - IGMP proxy configuration for multicast TV streaming:
  - Upstream interface (IPTV VLAN)
  - Downstream interface (LAN)
  - Multicast group management

### Using the Configuration Files

1. **Backup your current configuration:**
   ```bash
   cd /etc/config/
   cp network network.bak
   cp firewall firewall.bak
   cp dhcp dhcp.bak
   cp igmpproxy igmpproxy.bak
   ```

2. **Copy the configuration files:**
   ```bash
   # Copy files from this repository to your router
   scp config/network root@192.168.8.1:/etc/config/
   scp config/firewall root@192.168.8.1:/etc/config/
   scp config/dhcp root@192.168.8.1:/etc/config/
   scp config/igmpproxy root@192.168.8.1:/etc/config/
   ```

3. **Apply the configurations:**
   ```bash
   uci commit network
   uci commit firewall
   uci commit dhcp
   uci commit igmpproxy

   /etc/init.d/network reload
   /etc/init.d/firewall reload
   /etc/init.d/dnsmasq reload
   /etc/init.d/igmpproxy restart
   ```

**Note:** Remember to update the ULA prefix in the network configuration and the IPTV gateway address as described in the setup guide.

---

## References and Resources

This guide was created based on the following resources and community contributions:

### Official OpenWrt Documentation
- [OpenWrt ISP Configurations - Netherlands](https://openwrt.org/docs/guide-user/network/wan/isp-configurations#netherlands) - Official KPN FTTH configuration guide
- [OpenWrt UDP Multicast Documentation](https://openwrt.org/docs/guide-user/network/wan/udp_multicast#iptvudp_multicast) - Official multicast and IPTV configuration
- [OpenWrt VLAN Switch Configuration](https://openwrt.org/docs/guide-user/network/vlan/switch_configuration) - VLAN setup and switch configuration
- [OpenWrt UDP Multicast Guide](https://openwrt.org/docs/guide-user/network/wan/udp_multicast) - Comprehensive multicast configuration

### Community Guides and Tutorials
- [Daniel's GL.iNet GL-MT6000 on KPN Fiber Guide](https://danieldk.eu/GL.iNet-GL-MT6000-on-KPN-fiber) - Detailed KPN fiber setup with GL.iNet routers
- [Why Not Hugo - OpenWrt with KPN Fibre Optics](https://whynothugo.nl/journal/2023/02/11/openwrt-with-kpn-fibre-optics/) - KPN fiber configuration tutorial
- [BenWeb - KPN Glasvezel with LEDE OpenWrt Fork](https://www.benweb.eu/articles/technical/kpn-glasvezel-met-lede-openwrt-fork/) - KPN fiber setup guide
- [Arne Mauer's Network Configuration](https://arnemauer.nl/) - Network configuration resources

### Community Forums and Discussions
- [Tweakers Forum - KPN Fiber Discussion](https://gathering.tweakers.net/forum/list_message/60796348#60796348) - Community discussion on KPN fiber setup
- [Tweakers Forum - Additional KPN Configuration](https://gathering.tweakers.net/forum/list_message/52560251#52560251) - More KPN fiber configuration tips
- [OpenWrt Forum - IGMP Proxy Multiple Clients](https://forum.openwrt.org/t/igmpproxy-strange-behaviour-with-multiple-clients/19609/5) - IGMP proxy troubleshooting
- [OpenWrt Forum - IPTV IGMP Proxy Firewall Issues](https://forum.openwrt.org/t/solved-iptv-igmpproxy-and-firewall-issues/12890/16) - Firewall configuration for IPTV

### Acknowledgments
Special thanks to the OpenWrt community and the authors of the referenced guides for their contributions to the OpenWrt ecosystem and KPN FTTH configuration knowledge.

---

## Conclusion

Congratulations! You now have a fully configured Flint 3 router with:
- ✅ Working internet connection via PPPoE on VLAN 6
- ✅ Proper VLAN configuration for all ports
- ✅ AdGuard Home DNS filtering and security
- ✅ IGMP proxy for multicast TV streaming
- ✅ IPTV working on all LAN ports
- ✅ Complete firewall and DHCP configuration

Your KPN FTTH setup is now complete and ready for both internet and IPTV services. The configuration provides optimal performance, security, and reliability for your network.

### Optional Next Steps

If you want to extend your setup further, consider:
- **Wi-Fi IPTV:** Configure IPTV over wireless for additional TV+ Boxes
- **Guest Network:** Set up isolated guest Wi-Fi access
- **VPN Configuration:** Add VPN services for enhanced security
- **Advanced AdGuard Home:** Configure custom filtering rules and whitelists
