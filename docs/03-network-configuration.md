# Core Network Configuration

## Backup and Prepare Network Configuration

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

## Apply New Network Configuration

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

## Verify Internet Connection

1. **Check Connection Status:**
   - Return to the GL.iNet web interface
   - Navigate to **INTERNET** section
   - Verify that you have an active internet connection

2. **Test Internet Access:**
   - Open a web browser and test basic internet connectivity
   - Run speed tests to verify performance
   - Check that both IPv4 and IPv6 are working properly

## Configure Additional Settings

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