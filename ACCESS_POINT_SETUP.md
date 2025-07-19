# Flint 3 Access Point Setup Guide

## Introduction

This guide explains how to configure a second Flint 3 router as an Access Point (AP) to extend your Wi-Fi coverage. When configured as an Access Point, the router acts as a wireless bridge, extending your main network's coverage without creating a separate network segment.

**Benefits of Access Point Mode:**
- Extended Wi-Fi coverage throughout your home
- Seamless roaming between access points
- Single network management
- Shared internet and IPTV access
- No additional NAT or routing complexity

---

## Prerequisites

### Hardware Requirements
- **Main Router:** Flint 3 configured as per the main setup guide
- **Access Point Router:** Second Flint 3 router (or compatible GL.iNet device)
- **Ethernet Cable:** To connect the access point to the main router
- **Computer:** For configuration access

### Network Information
- **Main Router IP:** `192.168.8.1`
- **Network Range:** `192.168.8.0/24`
- **Wi-Fi SSID and Password:** Same as main router
- **Reserved IP for AP:** `192.168.8.2` (recommended)

---

## Part 1: Initial Access Point Configuration

### 1.1 Connect and Access the Second Router

1. **Connect Hardware:**
   - Connect the second Flint 3 router to power
   - Connect an Ethernet cable from its WAN port to a LAN port on the main router

2. **Initial Wi-Fi Setup:**
   - Scan the QR code on the bottom of the router or manually connect to the default Wi-Fi network
   - Follow the setup wizard to configure:
     - Admin password for the router
     - Wi-Fi SSID and password
     - Basic network settings

3. **Access Router Interface:**
   - Connect your computer to the router via Wi-Fi
   - Open a web browser and navigate to `192.168.8.1`
   - Login with the admin credentials you set during the wizard

### 1.2 Enable Access Point Mode

1. **Navigate to Network Settings:**
   - In the GL.iNet web interface, go to **NETWORK**
   - Look for **Network Mode** section

2. **Select Access Point Mode:**
   - Click on **Access Point** option
   - Confirm the mode change when prompted

3. **Important Notice:**
   > **⚠️ Important:** After bridging is successful, your router will automatically obtain an IP address from the main router. If you need to access this router's UI, you must connect using the IP address that was issued by the main router. To get this IP, check the connected clients list in your main router's UI. If you do not have access to the main router's UI, you can restore the routing mode by holding down the reset button for 4 seconds.

4. **Wait for Configuration:**
   - The router will automatically configure itself as an access point
   - Wait for the process to complete (1-2 minutes)
   - The router will reboot automatically

---

## Part 2: Find the Access Point's New IP Address

### 2.1 Check Main Router's Client List

1. **Access Main Router:**
   - Open a web browser and navigate to `192.168.8.1`
   - Login to the main router's web interface

2. **Navigate to Clients:**
   - Go to **CLIENTS** section
   - Look for a device with name similar to `GL-BE9300` or your router's model name
   - **Note the IP address** assigned to the access point router
   - **Note the MAC address** of the access point router

3. **Access the Access Point:**
   - Open a new browser tab
   - Navigate to the IP address you noted in the previous step
   - Login with the same admin credentials

---

## Part 3: Configure Wi-Fi Settings

### 3.1 Match Wi-Fi Configuration

1. **Navigate to Wireless Settings:**
   - In the access point router's web interface, go to **WIRELESS**

2. **Configure different Band:**
   - Set **SSID** to match your main router's SSID's
   - Set **Password** to match your main router's password's
   - Ensure **Security Mode** matches
   - Save the configuration

4. **Apply Changes:**
   - Click **Apply** to save all wireless settings
   - The access point will restart the wireless services

**Note Access Point Guest Network** In the GUI its not possible to set Guest SSID's, do this in LuCI if needed

---

## Part 3.5: Configure Wi-Fi Roaming (802.11r Fast Transition)

### 3.5.1 Enable Roaming on Main Router

1. **Access LuCI Interface:**
   - In the main router's GL.iNet web interface, go to **SYSTEM** → **Advanced Settings**
   - Click **"Go To LuCI"** to access the full OpenWrt configuration interface
   - Login with your admin password

2. **Navigate to Wireless Configuration:**
   - Go to **Network** → **Wireless**
   - Click on **Edit** for the wireless network you want to configure for roaming

3. **Configure Interface Settings:**
   - Click on the **Interface Configuration** tab
   - Navigate to the **WLAN Roaming** tab

4. **Enable 802.11r Fast Transition:**
   - Check the box for **802.11r Fast Transition**
   - Set **Mobility Domain** to a unique value (e.g., `1234`)
   - Set **R0 Key Lifetime** to `10000` (default value)
   - Set **R1 Key Holder** to `000000000000` (default value)
   - Set **Reassociation Deadline** to `1000` (default value)

5. **Save Configuration:**
   - Click **Save & Apply** to apply the changes

6. **Repeat for All SSIDs:**
   - Repeat steps 2-5 for each wireless network
   - Use the same **Mobility Domain** value for all networks on the same router

### 3.5.2 Enable Roaming on Access Point Router

1. **Access LuCI Interface:**
   - In the access point router's web interface, go to **SYSTEM** → **Advanced Settings**
   - Click **"Go To LuCI"** to access the full OpenWrt configuration interface
   - Login with your admin password

2. **Navigate to Wireless Configuration:**
   - Go to **Network** → **Wireless**
   - Click on **Edit** for the wireless network you want to configure for roaming

3. **Configure Interface Settings:**
   - Click on the **Interface Configuration** tab
   - Navigate to the **WLAN Roaming** tab

4. **Enable 802.11r Fast Transition:**
   - Check the box for **802.11r Fast Transition**
   - Set **Mobility Domain** to the **same value** as the main router (e.g., `1234`)
   - Set **R0 Key Lifetime** to `10000` (default value)
   - Set **R1 Key Holder** to `000000000000` (default value)
   - Set **Reassociation Deadline** to `1000` (default value)

5. **Save Configuration:**
   - Click **Save & Apply** to apply the changes

6. **Repeat for All SSIDs:**
   - Repeat steps 2-5 for each wireless network
   - Use the same **Mobility Domain** value for all networks on the access point

### 3.5.3 Roaming Configuration Details

**What is 802.11r Fast Transition?**
- **802.11r** is a Wi-Fi standard that enables fast roaming between access points
- It allows devices to quickly switch between access points without re-authentication
- Reduces connection interruption during roaming from ~100ms to ~10ms

**Key Configuration Parameters:**
- **Mobility Domain:** Must be identical across all access points in the roaming network
- **R0 Key Lifetime:** How long the PMK-R0 key is valid (default: 10000 seconds)
- **R1 Key Holder:** MAC address of the key holder (default: 000000000000)
- **Reassociation Deadline:** Maximum time for reassociation (default: 1000 milliseconds)

**Benefits of 802.11r:**
- Faster handoffs between access points
- Reduced connection drops during roaming
- Better performance for VoIP and video calls
- Improved user experience for mobile devices

### 3.5.4 Verify Roaming Configuration

1. **Check Configuration:**
   - In LuCI, go to **Network** → **Wireless**
   - Verify that 802.11r is enabled for all networks
   - Confirm Mobility Domain values match across routers

2. **Test Roaming:**
   - Connect a device to the Wi-Fi network
   - Walk between the main router and access point
   - Monitor for seamless handoffs without connection drops
   - Use a Wi-Fi analyzer app to verify the device switches access points

3. **Monitor Logs:**
   ```bash
   # Check for roaming events in the system logs
   logread | grep -i roam
   logread | grep -i 802.11r
   ```

---

## Part 4: Set Reserved IP Address

### 4.1 Configure Static IP Reservation

1. **Access Main Router:**
   - Go back to the main router's web interface (`192.168.8.1`)

2. **Navigate to Network Settings:**
   - Go to **NETWORK** → **LAN**
   - Scroll down to find **Address Reservation** section

3. **Add Static Reservation:**
   - Click **Add** button
   - In the **MAC Address** dropdown, select the access point router's MAC address
   - Set **IP Address** to `192.168.8.2`
   - Set **Description** to "Flint 3 Access Point" or similar
   - Click **Save**

### 4.2 Apply the Reservation

1. **Restart Access Point:**
   - Go back to the access point router's web interface
   - Navigate to **SYSTEM** → **Reboot**
   - Click **Reboot** to restart the access point

2. **Verify New IP:**
   - After reboot, the access point should now have IP `192.168.8.2`
   - You can verify this in the main router's **CLIENTS** section
   - The access point should now be accessible at `192.168.8.2`

---

## Part 5: Testing and Verification

### 5.1 Test Wi-Fi Connectivity

1. **Connect to Wi-Fi:**
   - Connect a device to the Wi-Fi network
   - Verify you can access the internet
   - Test connection to both 2.4GHz and 5GHz bands

2. **Test Roaming:**
   - Walk between the main router and access point
   - Verify seamless handoff between access points
   - Check that your device maintains internet connectivity
   - Monitor for any connection drops during handoffs
   - Use a Wi-Fi analyzer app to verify access point switching

3. **Advanced Roaming Test:**
   - Start a continuous ping to test connection stability:
     ```bash
     ping -t 8.8.8.8
     ```
   - Walk between access points while monitoring ping response times
   - Verify no packet loss during handoffs
   - Test with bandwidth-intensive applications (video calls, streaming)

### 5.2 Test IPTV (if applicable)

1. **Connect TV+ Box:**
   - Connect a KPN TV+ Box to the access point router's LAN port
   - Verify the TV+ Box receives an IP address from `192.168.8.x` range
   - Test live TV functionality

2. **Wireless IPTV:**
   - If using wireless TV+ Box, connect it to the access point's Wi-Fi
   - Verify IPTV functionality works properly

---

## Part 6: Advanced Configuration (Optional)

### 6.1 Optimize Wi-Fi Channels

1. **Check Channel Congestion:**
   - Use a Wi-Fi analyzer app to check channel usage
   - Ensure main router and access point use different channels

2. **Configure Channels:**
   - In the access point router's **WIRELESS** settings
   - Set 2.4GHz to a different channel than main router (e.g., 1, 6, or 11)
   - Set 5GHz to a different channel than main router

### 6.2 Configure Transmit Power

1. **Adjust Power Settings:**
   - In **WIRELESS** settings, look for **Transmit Power** option
   - Set to appropriate level (usually 20-23 dBm for home use)
   - Higher power may cause interference, lower power may reduce coverage

### 6.3 Mesh Networking Limitations

**Important:** The Flint 3 router uses a Broadcom chipset, which has limitations for mesh networking in OpenWrt.

#### Why Mesh Isn't Supported

1. **Broadcom Chipset Limitations:**
   - The Flint 3 uses a Broadcom BCM6756 chipset
   - Broadcom chipsets have limited support for 802.11s mesh networking in OpenWrt
   - Driver limitations prevent proper mesh protocol implementation

2. **802.11s Mesh Protocol:**
   - 802.11s is the IEEE standard for wireless mesh networking
   - Requires specific hardware and driver support
   - Not all Wi-Fi chipsets support mesh networking features

3. **OpenWrt Mesh Documentation:**
   - [OpenWrt 802.11s Mesh Guide](https://openwrt.org/docs/guide-user/network/wifi/mesh/802-11s)
   - [OpenWrt 80211s Mesh Configuration](https://openwrt.org/docs/guide-user/network/wifi/mesh/80211s)

#### Alternatives to Mesh Networking

**1. Access Point Mode (Current Setup)**
- **Advantages:**
  - Works with any OpenWrt-compatible router
  - Simple configuration and management
  - Reliable performance with wired backhaul
  - Supports all features including IPTV and VLANs
- **Disadvantages:**
  - Requires Ethernet cable connection
  - No wireless backhaul option

**2. Wireless Bridge Mode**
- **Configuration:**
  - Configure access point router in wireless bridge mode
  - Connect to main router's Wi-Fi network
  - Extend network without Ethernet cable
- **Limitations:**
  - Reduced bandwidth due to wireless backhaul
  - May not support all VLAN features
  - More complex configuration

**3. WDS (Wireless Distribution System)**
- **What it is:**
  - Older wireless bridging technology
  - Limited support in modern OpenWrt builds
  - Not recommended for new deployments

**4. Alternative Hardware Options**
- **Qualcomm-based routers:** Better mesh support in OpenWrt
- **MediaTek-based routers:** Good mesh compatibility
- **Dedicated mesh systems:** Commercial solutions like Google WiFi, Eero, etc.

#### Recommended Approach

For the Flint 3 router, the **Access Point mode** (as configured in this guide) is the best solution because:

1. **Reliability:** Wired backhaul provides stable, high-bandwidth connection
2. **Feature Support:** Full support for IPTV, VLANs, and advanced features
3. **Performance:** No bandwidth reduction from wireless backhaul
4. **Simplicity:** Easy to configure and maintain
5. **Cost-Effective:** Uses existing hardware without additional purchases

#### Future Considerations

If you need wireless backhaul or true mesh networking:

1. **Research Compatible Hardware:**
   - Look for routers with Qualcomm or MediaTek chipsets
   - Check OpenWrt hardware compatibility lists
   - Verify mesh support before purchase

2. **Consider Commercial Solutions:**
   - Dedicated mesh systems may offer better performance
   - However, they lack the advanced features of OpenWrt

3. **Hybrid Approach:**
   - Use access points for critical areas (wired backhaul)
   - Add wireless bridges for remote locations if needed

---

## Troubleshooting

### Common Issues and Solutions

#### Access Point Not Getting IP Address
1. **Check Physical Connection:**
   - Ensure Ethernet cable is connected to LAN port, not WAN port
   - Try a different Ethernet cable
   - Check that main router's DHCP is working

2. **Reset Access Point:**
   - Hold reset button for 4 seconds to restore routing mode
   - Reconfigure as access point

#### Wi-Fi Not Working
1. **Check SSID and Password:**
   - Ensure SSID and password exactly match main router
   - Check security mode compatibility

2. **Check Channel Settings:**
   - Ensure channels don't conflict with main router
   - Use Wi-Fi analyzer to find optimal channels

#### Cannot Access Access Point Web Interface
1. **Find Correct IP:**
   - Check main router's **CLIENTS** section for access point IP
   - Try accessing via the assigned IP address

2. **Check Network Connection:**
   - Ensure you're connected to the same network
   - Try connecting directly to access point's Wi-Fi

#### IPTV Not Working Through Access Point
1. **Check VLAN Configuration:**
   - Ensure access point is properly bridging VLAN traffic
   - Verify IGMP proxy is working on main router

2. **Test with Wired Connection:**
   - Connect TV+ Box directly to access point's LAN port
   - If wired works but wireless doesn't, check Wi-Fi multicast settings

#### Roaming Not Working Properly
1. **Check 802.11r Configuration:**
   - Verify 802.11r is enabled on both routers
   - Ensure Mobility Domain values match exactly
   - Check that all SSIDs have the same roaming configuration

2. **Verify Client Support:**
   - Not all devices support 802.11r Fast Transition
   - Test with different devices (phones, laptops, tablets)
   - Check device specifications for 802.11r support

3. **Check Signal Overlap:**
   - Ensure sufficient signal overlap between access points
   - Use Wi-Fi analyzer to check signal strength
   - Adjust transmit power if needed

4. **Monitor System Logs:**
   ```bash
   # Check for roaming-related errors
   logread | grep -i roam
   logread | grep -i 802.11r
   logread | grep -i wlan
   ```

#### Mesh Networking Questions
1. **Why Can't I Enable Mesh Mode?**
   - The Flint 3 uses a Broadcom chipset with limited mesh support
   - Access Point mode is the recommended alternative
   - Check hardware compatibility before attempting mesh configuration

2. **Alternative to Wired Connection:**
   - Consider wireless bridge mode for remote locations
   - Research routers with better mesh support (Qualcomm/MediaTek)
   - Commercial mesh systems may be more suitable

3. **Performance Comparison:**
   - Wired Access Point: Best performance, full feature support
   - Wireless Bridge: Reduced bandwidth, limited features
   - True Mesh: Hardware dependent, not supported on Flint 3

---

## Configuration Summary

After completing this setup, you will have:

- ✅ **Main Router:** `192.168.8.1` - Primary router with KPN FTTH and IPTV
- ✅ **Access Point:** `192.168.8.2` - Secondary router extending Wi-Fi coverage
- ✅ **Unified Network:** Single `192.168.8.0/24` network with seamless roaming
- ✅ **802.11r Fast Transition:** Enabled for fast roaming between access points
- ✅ **Shared Services:** Internet, IPTV, and AdGuard Home accessible from both routers
- ✅ **Extended Coverage:** Improved Wi-Fi coverage throughout your home

### Network Topology
```
KPN ONT → Main Router (192.168.8.1) → Access Point (192.168.8.2)
                ↓                           ↓
            Wi-Fi Network              Wi-Fi Network
         (Same SSID/Password)        (Same SSID/Password)
```

---

## Maintenance and Management

### Regular Tasks
1. **Monitor Performance:**
   - Check signal strength and connection quality
   - Monitor for interference or congestion

2. **Update Firmware:**
   - Keep both routers updated with latest firmware
   - Update access point firmware through its web interface

3. **Backup Configuration:**
   - Export configuration from both routers
   - Store backups in a safe location

### Adding More Access Points
- Follow the same process for additional routers
- Use different reserved IPs (e.g., `192.168.8.3`, `192.168.8.4`)
- Ensure proper channel planning for multiple access points

---

## Conclusion

Your Flint 3 Access Point setup is now complete! You have successfully extended your Wi-Fi coverage while maintaining a unified network with all the benefits of your main router's configuration, including IPTV support and AdGuard Home filtering.

The access point will provide seamless Wi-Fi coverage and can handle both regular internet traffic and IPTV streaming, making it perfect for homes with multiple TV+ Boxes or areas that need extended coverage.
