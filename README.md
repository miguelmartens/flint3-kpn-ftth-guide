# Flint 3 OpenWrt Router Setup Guide for KPN FTTH

## Introduction

This guide provides a complete setup for the GL.iNet Flint 3 router with OpenWrt to work with KPN FTTH internet and IPTV services. The Flint 3 is a compact, powerful router that offers excellent performance and flexibility for advanced networking configurations.

This setup will configure:
- **VLAN 6** for PPPoE Internet connection
- **VLAN 4** for KPN IPTV (multicast) services
- **IGMP proxy** for multicast TV streaming
- **AdGuard Home** for DNS filtering and security

The configuration uses the default IP range of `192.168.8.x` and provides both wired and wireless IPTV options.

**Note:** In earlier attempts there was a dedicated port (LAN4 / port 7) for IPTV, but now it's working over all the LAN ports same as the default KPN ExperiaBox.

## Quick Start

1. **[Prerequisites](docs/01-prerequisites.md)** - Check requirements and prepare your setup
2. **[Initial Router Setup](docs/02-initial-setup.md)** - Basic router configuration and SSH access
3. **[Network Configuration](docs/03-network-configuration.md)** - Core network and VLAN setup
4. **[Advanced Configuration](docs/04-advanced-configuration.md)** - Technical details and explanations
5. **[AdGuard Home Setup](docs/05-adguard-home.md)** - DNS filtering and ad blocking
6. **[IPTV Setup](docs/06-iptv-setup.md)** - IPTV configuration and testing

**Optional:**
7. **[Access Point Setup](docs/07-access-point-setup.md)** - Extend Wi-Fi coverage with a second Flint 3

## What You'll Get

After completing this setup, you'll have:

✅ **Working internet connection** via PPPoE on VLAN 6
✅ **Proper VLAN configuration** for all ports
✅ **AdGuard Home DNS filtering** and security
✅ **IGMP proxy** for multicast TV streaming
✅ **IPTV working** on all LAN ports
✅ **Complete firewall and DHCP** configuration

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

## Additional Resources

- **[Access Point Setup Guide](docs/07-access-point-setup.md)** - Configure a secondary Flint 3 as an access point
- **[Configuration Files](config/)** - Ready-to-use configuration files for your router

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

## Support

If you encounter issues during setup:
1. Check the troubleshooting sections in each guide
2. Review the references above for additional resources
3. Ensure all prerequisites are met before starting

## Conclusion

Your KPN FTTH setup will be complete and ready for both internet and IPTV services. The configuration provides optimal performance, security, and reliability for your network.

### Optional Next Steps

If you want to extend your setup further, consider:
- **Wi-Fi IPTV:** Configure IPTV over wireless for additional TV+ Boxes (optional and not covered)
- **Guest Network:** Set up isolated guest Wi-Fi access
- **VPN Configuration:** Add VPN services for enhanced security
- **Advanced AdGuard Home:** Configure custom filtering rules and whitelists
- **Access Point:** Add a secondary Flint 3 router as Access Point for more coverage - [See Access Point Setup Guide](docs/07-access-point-setup.md)
