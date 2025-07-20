# AdGuard Home Setup

## Install and Enable AdGuard Home

1. **Navigate to Applications:**
   - In the GL.iNet web interface, go to **APPLICATIONS**
   - Find **AdGuard Home** and click on it

2. **Enable AdGuard Home:**
   - Toggle the switch to enable AdGuard Home
   - Wait for the service to start

3. **Optional: Enable Client Request Handling:**
   - Enable "AdGuard Home Handle Client Requests" for enhanced functionality
   - This provides better client identification and filtering

## Access AdGuard Home Interface

1. **Open AdGuard Home:**
   - Click on the AdGuard Home interface link
   - This will open the full AdGuard Home web interface

2. **Initial Setup:**
   - Follow the AdGuard Home setup wizard
   - Configure admin credentials
   - Set up basic filtering rules and configure DNS
   - Make sure Parallel requests is selected
   - Enable DNSSEC

## Important DNS Configuration Notice

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

## Configure DNS Blocklists

**Recommended Blocklist Configuration:**

For optimal protection, it's recommended to use these blocklists in combination. Each serves a specific purpose and provides comprehensive coverage:

1. **Navigate to AdGuard Home Interface:**
   - Open the AdGuard Home web interface
   - Go to **Filters** → **DNS blocklists**

2. **Add Recommended Blocklists:**

   **AdGuard DNS filter:**
   - **URL:** `https://adguardteam.github.io/AdGuardSDNSFilter/Filters/filter.txt`
   - **Purpose:** Core ad blocking and tracking protection
   - **Type:** Ad blocking and privacy

   **HaGeZi's Threat Intelligence Feeds:**
   - **URL:** `https://raw.githubusercontent.com/hagezi/dns-blocklists/main/domains/light.txt`
   - **Purpose:** Blocks malicious domains, phishing, and malware
   - **Type:** Security and threat protection

   **HaGeZi's Pro++ Blocklist:**
   - **URL:** `https://raw.githubusercontent.com/hagezi/dns-blocklists/main/domains/pro.plus.txt`
   - **Purpose:** Comprehensive blocking including ads, tracking, and unwanted content
   - **Type:** Enhanced privacy and content filtering

   **Dandelion Sprout's Anti-Malware List:**
   - **URL:** `https://raw.githubusercontent.com/DandelionSprout/adfilt/master/GameConsoleAdblockList.txt`
   - **Purpose:** Blocks malware, scam sites, and malicious domains
   - **Type:** Security and malware protection

3. **Blocklist Configuration Tips:**
   - **Update Frequency:** Set all blocklists to update every 24 hours
   - **Enabled Status:** Ensure all blocklists are enabled after adding
   - **Whitelist Management:** Monitor for false positives and add necessary whitelist entries
   - **Performance:** These blocklists are optimized for performance and won't significantly impact DNS resolution speed

4. **Verify Blocklist Functionality:**
   - Test ad blocking by visiting known ad-heavy websites
   - Check the AdGuard Home dashboard for blocked queries
   - Monitor the logs for any issues with blocklist updates

**Blocklist Sources and Information:**
- **HaGeZi's DNS Blocklists:** [GitHub Repository](https://github.com/hagezi/dns-blocklists)
- **AdGuard DNS Filter:** [Official Documentation](https://adguard-dns.io/en/public-dns.html)
- **Dandelion Sprout's Lists:** [GitHub Repository](https://github.com/DandelionSprout/adfilt)

**Performance Considerations:**
- These blocklists are regularly updated and optimized for performance
- Combined, they provide comprehensive protection without significant latency impact
- Monitor system resources if you add additional large blocklists
- Consider your network's specific needs when adding extra blocklists

## Test Ad Blocking Effectiveness

After configuring your blocklists, it's important to test their effectiveness. Here are recommended testing tools to verify your AdGuard Home setup:

**Recommended Testing Tools:**

1. **[Can You Block It](https://canyoublockit.com/)**
   - **Simple Test:** Basic self-hosted advertisements
   - **eXtreme Test:** Various ad formats from popular ad networks
   - **Advanced Test:** Independent diagnosis of different ad formats
   - **Best for:** Quick verification of basic ad blocking functionality

2. **[AdBlock Tester](https://adblock-tester.com/)**
   - **Comprehensive Testing:** Tests contextual advertising, analytics tools, banner advertising, and error monitoring
   - **Detailed Results:** Provides specific scores and detailed breakdowns
   - **Multiple Categories:** Covers Google AdSense, Yandex Direct, Google Analytics, Hotjar, and more
   - **Best for:** Detailed analysis of different ad and tracking types

3. **[AdBlock Turtlecute](https://adblock.turtlecute.org/)**
   - **Advanced Testing:** Tests cosmetic filtering, ad script loading, and host blocking
   - **Compatibility Table:** Shows tested browser-adblock combinations
   - **Real-time Results:** Live testing with immediate feedback
   - **Best for:** Advanced users wanting detailed technical analysis

**Testing Procedure:**

1. **Before Testing:**
   - Ensure AdGuard Home is running and all blocklists are updated
   - Clear your browser cache to avoid false results
   - Disable any browser-based ad blockers to test only AdGuard Home

2. **Run Tests:**
   - Start with [Can You Block It](https://canyoublockit.com/) for basic verification
   - Use [AdBlock Tester](https://adblock-tester.com/) for comprehensive analysis
   - Try [AdBlock Turtlecute](https://adblock.turtlecute.org/) for advanced testing

3. **Interpret Results:**
   - **Good Results:** Most ads and trackers should be blocked
   - **Expected Scores:** 80-100% blocking rate is excellent
   - **False Positives:** Some legitimate services might be blocked (add to whitelist if needed)

**Troubleshooting Common Issues:**

- **Low Blocking Scores:** Check if blocklists are properly loaded and updated
- **False Positives:** Add legitimate domains to AdGuard Home whitelist
- **Performance Issues:** Monitor AdGuard Home logs for any errors
- **Browser Cache:** Clear cache and cookies before testing

**Additional Testing Tips:**

- Test on different devices and browsers on your network
- Monitor AdGuard Home dashboard during testing to see blocked queries
- Check AdGuard Home logs for any configuration issues
- Consider testing at different times to ensure consistent performance

**Expected Results:**
With the recommended blocklist configuration, you should achieve:
- **Ad Blocking:** 90-100% success rate on most ad formats
- **Tracking Protection:** Strong protection against analytics and tracking
- **Malware Protection:** Effective blocking of malicious domains
- **Performance:** Minimal impact on browsing speed 