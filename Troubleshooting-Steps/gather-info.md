# Gathering Information

**Date:** 2024-10-27
**Time:** 14:35 PST

**Initial Steps:**

1.  **Verification of Reported Issue:**
    * Attempted to access the website from an external network (using a mobile hotspot). Confirmed the "connection timed out" error.
    * Attempted to access the website from an internal network (using a workstation on a different subnet). Confirmed the same error.
2.  **User Reports:**
    * Contacted several users who reported the issue. All confirmed they were unable to access the website and received "connection timed out" errors.
    * Asked if any users experienced intermittent connectivity or slow loading times prior to the complete outage. Some users reported slow loading times in the minutes leading up to the complete outage.
3.  **Network Monitoring Tools:**
    * Checked the network monitoring dashboard. Observed a sharp drop in network traffic to `webserver01` at approximately 14:30 PST.
    * Observed a spike in latency for `webserver01` just prior to the complete traffic drop.
    * Screenshot of network monitoring dashboard: `![Network Monitoring Dashboard](monitoring-tool.png)`
    * Observed that the port on the access switch that `webserver01` is plugged into, was showing as down.
4.  **Recent Changes:**
    * Reviewed the change management logs for any recent network or server changes.
    * Confirmed that no scheduled maintenance or configuration changes were performed on `webserver01` or any related network devices in the past 24 hours.
5.  **Physical Inspection (Initial):**
    * Visually inspected `webserver01` in rack A. The server was powered on, and the link lights on the network interface card (NIC) were not illuminated.
    * Visually inspected the access switch in the same rack. The port connected to `webserver01` had no link lights.

**Next Steps:**

* Proceed to check the physical connections in more detail.
* Use network diagnostic tools to further investigate the connectivity issue.
* Analyze network logs to identify any potential errors.
* Isolate the problem by testing different network components.

**Detailed Next Steps:**

1.  **Detailed Physical Connection Checks:**
    * Verify the network cable integrity by checking for any visible damage or kinks.
    * Reseat the network cable at both the server NIC and the switch port.
    * Test the cable with a cable tester if available.
        * Document the results of the cable test.
2.  **Network Diagnostic Tools:**
    * Use `ping` to test connectivity from various points in the network to `webserver01`.
        * Document the results of each ping test.
    * Use `traceroute` or `tracert` to trace the network path to `webserver01` and identify any points of failure.
        * Document the results of each traceroute/tracert test.
    * Use `ipconfig` (Windows) or `ifconfig` (Linux) on a local machine to check the IP configuration.
        * Document the IP configuration results.
    * Use `nslookup` to check DNS resolution for `webserver01`.
        * Document the results of the nslookup test.
3.  **Network Log Analysis:**
    * Examine the logs of the access switch connected to `webserver01` for any errors or warnings.
        * Save relevant log excerpts.
    * Check the logs of the core switch and firewall for any relevant entries.
        * Save relevant log excerpts.
    * Review the operating system logs on `webserver01` (if accessible) for network-related errors.
        * Save relevant log excerpts.
4.  **Problem Isolation:**
    * **Test Laptop Connection:**
        * Connected a test laptop directly to the switch port previously used by `webserver01`.
        * The laptop obtained an IP address and established network connectivity.
        * Documented the results: "Test laptop connected successfully to the switch port, indicating the switch port is functional."
    * **Known Good Cable Test:**
        * Replaced the existing network cable with a known good cable.
        * The issue persisted, indicating the cable was not the cause.
        * Documented the results: "Replaced cable with a known good cable, issue persists."
    * **Different Switch Port:**
        * Connected `webserver01` to a different switch port.
        * The issue persisted, indicating the problem was not isolated to the original switch port.
        * Documented the results: "Connected to a different switch port, issue persists."
    * **Different Server on Same Port:**
        * Connected a different server to the original switch port.
        * The different server experienced the same connectivity issues.
        * Documented the results: "Different server connected to the same port experiences connectivity issues."
    * **Switch Port Configuration:**
        * Examined the switch port configuration. Found that the port was administratively disabled.
        * Documented the switch port configuration: "Switch port configured as administratively down."
    * **Server NIC Configuration:**
        * Since the switch port was down, the server NIC configuration was not likely the culprit.
        * Documented: "Server NIC configuration not examined due to switch port being down."
    * **Server Firewall Configuration:**
        * Since the server was not reachable, the firewall configuration was not likely the culprit.
        * Documented: "Server firewall configuration not examined due to server not being reachable."
