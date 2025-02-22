# Traceroute/Tracert Tests

**Date:** 2024-10-27
**Time:** 15:00 PST

**Objective:** To trace the network path to `webserver01` (192.168.1.10) from various network locations and identify potential points of failure.

**Test 1: Traceroute from a Local Workstation (Same Subnet)**

* **Command:** `tracert 192.168.1.10` (Windows)
* **Output:**
    ```
    Tracing route to 192.168.1.10 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  192.168.1.1
      2     * * * Request timed out.
      3     * * * Request timed out.
      4     * * * Request timed out.
      ...
    ```
* **Interpretation:** The trace reaches the default gateway (192.168.1.1) but fails to proceed further, indicating a potential issue on the local subnet or between the workstation and `webserver01`.

**Test 2: Traceroute from the Core Switch**

* **Command:** `traceroute 192.168.1.10` (Executed from the core switch's CLI)
* **Output:**
    ```
    Type escape sequence to abort.
    Tracing the route to 192.168.1.10

      1  192.168.1.1 1 msec 0 msec 0 msec
      2  * * *
      3  * * *
      ...
    ```
* **Interpretation:** Similar to the local workstation, the trace stops at the first hop (the gateway), indicating an issue within the local network segment.

**Test 3: Traceroute from the Firewall**

* **Command:** `tracert 192.168.1.10` (Executed from the firewall's CLI)
* **Output:**
    ```
    Tracing route to 192.168.1.10 over a maximum of 30 hops

      1  [Firewall's Internal IP] 0 ms 0 ms 0 ms
      2  * * * Request timed out.
      3  * * * Request timed out.
      ...
    ```
* **Interpretation:** The firewall also fails to reach `webserver01`, further confirming the issue is within the local network.

**Test 4: Traceroute from Test Laptop (Directly Connected to Switch Port)**

* **Command:** `tracert 192.168.1.10` (Windows)
* **Output:**
    ```
    Tracing route to 192.168.1.10 over a maximum of 30 hops

      1  192.168.1.1 1 ms 1 ms 1 ms
      2  * * * Request timed out.
      3  * * * Request timed out.
      ...
    ```
* **Interpretation:** Even when directly connected to the switch port, the traceroute fails, solidifying that the issue is between the switch port and the server.

**Findings:**

* All traceroute tests fail to reach `webserver01`.
* The point of failure appears to be immediately after the first hop (the default gateway).
* This suggests a layer 2 issue or a problem with the access switch or the server's connection to it.

**Next Steps:**

* **Analyze the logs of the access switch for port status and errors.** Specifically, look for any indication of the port being disabled, errors related to spanning-tree, or MAC address table issues.
* **Continue examining the physical connections between the switch port and the server.** While visual inspection and cable testing have been performed, further tests might be needed, such as connecting a known-good device to the switch port to isolate the problem.
* **Further isolate the problem by testing with a known good cable and a different server on the same port.** This will help rule out cable issues and server-specific problems, respectively.
* **Examine the configuration of the switch port.** Verify that the port is enabled, configured for the correct VLAN, and that there are no access control lists (ACLs) blocking traffic.
* **If possible, perform a loopback test on the switch port.** This can help determine if the switch port itself is functioning correctly.
* **If spanning-tree is enabled, examine spanning-tree logs and configurations.** Spanning-tree issues can sometimes cause connectivity problems.
* **Check the ARP table on the switch and the router.** This can help determine if the MAC address of the server is being learned correctly. If there is no ARP entry, then the server is not communicating on the layer 2 network.
* **Check the MAC address table on the switch.** Verify that the MAC address of the server is associated with the correct port.
* **Check for any VLAN mismatches.** Ensure that the switch port and the server are both configured for the same VLAN.
* **Check for duplex mismatches.** Ensure that the switch port and the server are both configured for the same duplex setting (full or half).
* **Check for any errors on the switch port interface counters.** This can help identify issues such as CRC errors, runts, or giants.
* **If possible, perform a packet capture on the switch port.** This can provide detailed information about the network traffic and help identify any issues.
* **If possible, check the server's operating system logs for any network-related errors.** This can help determine if the issue is related to the server's operating system or network configuration.
* **Review the server NIC configuration.** Ensure that the NIC is enabled and configured correctly.

**Detailed Next Steps:**

1.  **Access Switch Log Analysis (Detailed):**
    * Connect to the access switch's CLI using SSH or console.
    * Use the command `show logging` or `show log` to view the switch's system logs.
    * Filter logs for the specific port connected to `webserver01` (e.g., `interface GigabitEthernet1/0/1`).
    * Look for:
        * Port status changes (up/down).
        * Spanning-tree protocol (STP) events.
        * MAC address learning errors.
        * Interface errors (CRC, runts, giants).
        * Authentication or security violations.
    * Save relevant log excerpts to a file (e.g., `access-switch-logs.txt`).

2.  **Physical Connection Verification (Advanced):**
    * **Known-Good Device Test:**
        * Connect a known-good laptop or test server to the switch port.
        * Attempt to ping and traceroute to a known working IP address.
        * Document the results (successful/failed connectivity).
    * **Cable Loopback Test (If Switch Supports):**
        * Configure the switch port for loopback testing (if supported).
        * Use the switch's diagnostic tools to perform a loopback test.
        * Document the test results (pass/fail).

3.  **Further Isolation Tests:**
    * **Known-Good Cable Test (Repeat with different cable):**
        * Try a different known good cable.
        * Document the results.
    * **Different Server on Same Port (Repeat with different server):**
        * Try a different server on the port.
        * Document the results.
    * **VLAN Testing:**
        * Verify the VLAN configuration on the switch port.
        * If possible, test with a test laptop configured for the same VLAN.

4.  **Switch Port Configuration Examination (Detailed):**
    * Use the command `show interface GigabitEthernet1/0/1` (or equivalent) to display the port's configuration.
    * Verify:
        * Port status (enabled/disabled).
        * VLAN membership.
        * Speed and duplex settings.
        * Access control lists (ACLs).
        * Spanning-tree settings.
        * Document the port configuration.

5.  **ARP and MAC Address Table Examination:**
    * Use the command `show arp` on the switch and the router to check for ARP entries related to `webserver01`.
    * Use the command `show mac address-table interface GigabitEthernet1/0/1` (or equivalent) to check the switch's MAC address table.
    * Verify that `webserver01`'s MAC address is present and associated with the correct port.

6.  **Interface Counter Examination:**
    * Use the command `show interface GigabitEthernet1/0/1 counters errors` (or equivalent) to display interface error counters.
    * Check for CRC errors, runts, giants, and other error types.
    * Document the error counters.

7.  **Packet Capture (If Possible):**
    * If the switch supports port mirroring or a similar feature, configure it to capture traffic on the port connected to `webserver01`.
    * Use a packet analyzer (e.g., Wireshark) to analyze the captured traffic.
    * Look for:
        * ARP requests and replies.
        * TCP or UDP traffic.
        * Error messages or retransmissions.
    * Save the packet capture to a file (e.g., `webserver01-capture.pcap`).

8.  **Server Operating System Log Examination (If Accessible):**
    * If `webserver01` is accessible via console or remote access, examine the operating system logs.
    * Look for:
        * Network interface errors.
        * Firewall logs.
        * System events related to network connectivity.
    * Save relevant log excerpts.

9.  **Server NIC Configuration Review:**
    * If `webserver01` is accessible, check the NIC configuration using `ipconfig` (Windows) or `ifconfig` (Linux).
    * Verify:
        * IP address, subnet mask, and gateway settings.
        * NIC status (enabled/disabled).
        * Driver version.
    * If possible, check the NIC's advanced settings for duplex, speed, and flow control.

10. **Server Firewall Configuration Review (If Applicable):**
    * If `webserver01` has a software firewall, review its configuration.
    * Verify that the firewall is not blocking necessary traffic.
    * Check for any recent firewall rule changes.

This detailed plan will help you methodically investigate the network outage and gather the necessary information to identify the root cause.
