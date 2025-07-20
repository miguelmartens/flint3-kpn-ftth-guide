# Initial Router Setup

## First-Time Hardware Setup

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

## Access LuCI Advanced Interface

1. **Navigate to LuCI:**
   - In the GL.iNet web interface, go to **SYSTEM** → **Advanced Settings**
   - Click **"Go To LuCI"** to access the full OpenWrt configuration interface
   - Login with the same admin password you set during the initial setup

2. **Important Notice - Switch Topology Warning:**
   > **⚠️ Warning:** You may see a message: "Switch switch0 has an unknown topology - the VLAN settings might not be accurate."
   >
   > This is a known issue with some OpenWrt builds and can be safely ignored for now. The switch0 configuration will be handled manually in the network configuration file.

## SSH Access Setup

1. **Enable SSH Access:**
   - In LuCI, go to **System** → **Administration** → **SSH Access**
   - Ensure SSH is enabled and configured for root access

2. **Connect via SSH:**
   ```bash
   ssh root@192.168.8.1
   ```
   - Use the same admin password you set during the initial setup 