# IPTV Setup and Configuration

## Install IGMP Proxy Plugin

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

## Get IPTV Gateway Information

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

## Apply Network Changes

**In the SSH terminal:**

1. **Commit and Reload Network:**
   ```bash
   uci commit network
   /etc/init.d/network reload
   ```

## Configure Firewall

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

## Configure DHCP

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

## Configure IGMP Proxy

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

## Verify Configuration in LuCI

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

## Final Router Reboot

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

## Test IPTV Connectivity

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

## Troubleshooting

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