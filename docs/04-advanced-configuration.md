# Advanced Configuration Sections

## IGMP Proxy and Snooping

**IGMP (Internet Group Management Protocol)** is essential for multicast traffic handling:

- **IGMP Snooping:** Allows the switch to monitor IGMP traffic and forward multicast packets only to ports that have requested them
- **IGMP Proxy:** Forwards IGMP messages between different network segments (essential for IPTV)

**Configuration Details:**
- IGMP snooping is enabled on the LAN bridge (`br-lan`)
- IGMP version 3 is enforced for better efficiency
- The proxy configuration handles upstream (IPTV VLAN) and downstream (LAN) traffic

## IPv6 Configuration

**IPv6 settings are automatically configured:**
- ULA (Unique Local Address) prefix is preserved from your original configuration
- DHCPv6 and RA (Router Advertisement) are enabled for automatic IPv6 address assignment
- IPv6 forwarding is properly configured for all interfaces

**Important IPv6 Reliability Note:**
The `wan6` interface uses `option device '@wan'` instead of `option interface 'wan'` for improved reliability. While both options work according to [OpenWrt documentation](https://openwrt.org/docs/guide-user/network/ipv6/configuration#native_ipv6_connection), using `@wan` (device reference) instead of `'wan'` (interface reference) provides better IPv6 connectivity recovery after fiber converter or ONT hiccups. This ensures IPv6 remains stable even when the connection temporarily drops and reconnects.

## MTU Settings

**MTU (Maximum Transmission Unit) optimization (Baby jumbo frames):**
- WAN interface: 1500 bytes (standard for PPPoE)
- VLAN interfaces: 1508 bytes (accounting for VLAN tag overhead)
- Physical interfaces: 1512 bytes (accounting for additional overhead)

These settings ensure optimal performance while avoiding packet fragmentation.

## VLAN Configuration Explanation

**VLAN Structure:**
- **VLAN 1:** Default LAN traffic (Ports 1, 2, 3)
- **VLAN 4:** IPTV traffic (Port 4 + CPU)
- **VLAN 6:** Internet traffic (WAN port + CPU)

**Port Tagging:**
- CPU ports are tagged (`t`) to handle multiple VLANs
- LAN ports are untagged for simple device connectivity
- WAN port is tagged for VLAN 6 (Internet)

## Switch Configuration

The following warning is displayed in LuCI when factory reset: "Switch switch0 has an unknown topology - the VLAN settings might not be accurate".

Your Flint 3 running OpenWrt shows two switch entries because one represents the SoC's internal switch managed via the newer DSA framework (switch0), and the other is the external switch chip still managed via the legacy swconfig subsystem (switch1). Luci flags switch0 as having "unknown topology" because the old swconfig-based UI cannot interpret DSA-managed switches.

Due to this I deleted the switch0 block entirely to avoid confusion in LuCI.

## Thanks

Special thanks to **Chris.nl** from the tweaker.net forum who pointed out the IPv6 reliability improvement using `option device '@wan'` instead of `option interface 'wan'` for the wan6 configuration. This small but important change ensures better IPv6 connectivity recovery after connection hiccups.
