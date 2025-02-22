# IP Configuration Checks

**Date:** 2024-10-27
**Time:** 15:15 PST

**Objective:** To verify the IP configuration of relevant devices and ensure they are within the expected network range.

**Test 1: Local Workstation IP Configuration**

* **Command (Windows):** `ipconfig /all`
* **Relevant Output:**
    ```
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . . . . . . : example.com
       Description . . . . . . . . . . . : Intel(R) Ethernet Connection (2) I219-LM
       Physical Address. . . . . . . . . : XX-XX-XX-XX-XX-XX
       DHCP Enabled. . . . . . . . . . . : Yes
       Autoconfiguration Enabled . . . . : Yes
       Link-local IPv6 Address . . . . . : fe80::XXXX:XXXX:XXXX:XXXX%12(Preferred)
       IPv4 Address . . . . . . . . . . . : 192.168.1.100(Preferred)
       Subnet Mask . . . . . . . . . . . : 255.255.255.0
       Default Gateway . . . . . . . . . : 192.168.1.1
       DHCP Server . . . . . . . . . . . : 192.168.1.1
       DNS Servers . . . . . . . . . . . : 192.168.1.1, 8.8.8.8
    ```
* **Interpretation:**
    * The workstation is configured with a valid IP address (192.168.1.100) within the expected subnet (192.168.1.0/24).
    * The default gateway (192.168.1.1) is correct.
    * DNS settings appear to be valid.

**Test 2: Test Laptop IP Configuration (Directly Connected to Switch Port)**

* **Command (Windows):** `ipconfig /all`
* **Relevant Output:**
    ```
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . . . . . . : example.com
       Description . . . . . . . . . . . : Realtek PCIe GbE Family Controller
       Physical Address. . . . . . . . . : YY-YY-YY-YY-YY-YY
       DHCP Enabled. . . . . . . . . . . : Yes
       Autoconfiguration Enabled . . . . : Yes
       Link-local IPv6 Address . . . . . : fe80::YYYY:YYYY:YYYY:YYYY%13(Preferred)
       IPv4 Address . . . . . . . . . . . : 192.168.1.101(Preferred)
       Subnet Mask . . . . . . . . . . . : 255.255.255.0
       Default Gateway . . . . . . . . . : 192.168.1.1
       DHCP Server . . . . . . . . . . . : 192.168.1.1
       DNS Servers . . . . . . . . . . . : 192.168.1.1, 8.8.8.8.
    ```
* **Interpretation:**
    * The test laptop received a valid IP address (192.168.1.101) via DHCP, confirming the switch port is functioning at Layer 2 and passing DHCP.
    * The subnet mask and default gateway are correct.
    * This indicates the switch port is able to provide network connectivity.

**Test 3: `webserver01` IP Configuration (If Accessible - Documented for Completeness)**

* **Command (Linux):** `ifconfig` or `ip addr show`
* **Expected Output (If Accessible):**
    ```
    eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 192.168.1.10  netmask 255.255.255.0  broadcast 192.168.1.255
            inet6 fe80::ZZZZ:ZZZZ:ZZZZ:ZZZZ  prefixlen 64  scopeid 0x20<link>
            ether AA:BB:CC:DD:EE:FF  txqueuelen 1000  (Ethernet)
            RX packets 0  bytes 0 (0.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    ```
* **Interpretation (If Accessible):**
    * Verifies the `webserver01` IP address, subnet mask, and gateway.
    * Checks the NIC status (UP/DOWN).
    * Checks for any interface errors.

**Findings:**

* The local workstation and test laptop IP configurations are valid.
* The test laptop's successful IP address acquisition confirms the switch port's basic functionality.
* Since `webserver01` is currently unreachable, its IP configuration cannot be directly verified at this time.
* Therefore, the problem is most likely a Layer 2 issue, or an issue with the server itself.

**Next Steps:**

* **Proceed with DNS checks to verify name resolution.**
    * Use `nslookup` or `dig` to test DNS resolution for `webserver01` from various network locations.
    * Check for any errors or inconsistencies in DNS responses.
    * Verify that the DNS server is reachable and functioning correctly.
* **Continue with log analysis to identify potential errors on the access switch.**
    * Examine the switch logs for any errors related to the port connected to `webserver01`.
    * Look for any indications of port status changes, spanning-tree issues, or MAC address learning problems.
    * Pay close attention to any error messages or warnings that might provide clues about the cause of the outage.
* **Further isolate the problem by checking ARP and MAC address tables on the switch.**
    * Use the `show arp` command on the switch to check for ARP entries related to `webserver01`.
    * Use the `show mac address-table` command to check if the switch has learned the MAC address of `webserver01`.
    * Verify that the MAC address is associated with the correct port.
    * This will help determine if the switch is able to communicate with `webserver01` at Layer 2.
* **Examine the switch port configuration in detail.**
    * Use the `show interface` command to display the configuration of the port connected to `webserver01`.
    * Verify that the port is enabled, configured for the correct VLAN, and that there are no access control lists (ACLs) blocking traffic.
    * Check for any errors or misconfigurations in the port's settings.
* **If possible, perform a loopback test on the switch port.**
    * This can help determine if the switch port itself is functioning correctly.
    * Configure the port for loopback testing and use the switch's diagnostic tools to perform the test.
* **If spanning-tree is enabled, examine spanning-tree logs and configurations.**
    * Spanning-tree issues can sometimes cause connectivity problems.
    * Check for any spanning-tree events or errors in the switch logs.
    * Verify that the spanning-tree configuration is correct.
* **Check for duplex mismatches.**
    * Ensure the switch port and the server NIC are configured for the same duplex (full or half).
    * Duplex mismatches can cause severe performance issues.
* **Examine the switch port interface counters.**
    * Use the `show interface counters errors` command to check for interface errors.
    * Look for CRC errors, runts, giants, and other error types.
    * These errors can indicate physical layer problems or cable issues.
* **If possible, perform a packet capture on the switch port.**
    * This can provide detailed information about network traffic.
    * Use a packet analyzer (e.g., Wireshark) to analyze the captured traffic.
    * Look for ARP requests/replies, TCP/UDP traffic, and error messages.
* **If possible, check the server's operating system logs for network errors.**
    * If `webserver01` is accessible via console or remote access, check system logs.
    * Look for network interface errors, firewall logs, and system events related to network connectivity.

**Next Steps:**

* **Proceed with DNS checks to verify name resolution.**
    * Use `nslookup` or `dig` to test DNS resolution for `webserver01` from various network locations.
    * Check for any errors or inconsistencies in DNS responses.
    * Verify that the DNS server is reachable and functioning correctly.
* **Continue with log analysis to identify potential errors on the access switch.**
    * Examine the switch logs for any errors related to the port connected to `webserver01`.
    * Look for any indications of port status changes, spanning-tree issues, or MAC address learning problems.
    * Pay close attention to any error messages or warnings that might provide clues about the cause of the outage.
* **Further isolate the problem by checking ARP and MAC address tables on the switch.**
    * Use the `show arp` command on the switch to check for ARP entries related to `webserver01`.
    * Use the `show mac address-table` command to check if the switch has learned the MAC address of `webserver01`.
    * Verify that the MAC address is associated with the correct port.
    * This will help determine if the switch is able to communicate with `webserver01` at Layer 2.
* **Examine the switch port configuration in detail.**
    * Use the `show interface` command to display the configuration of the port connected to `webserver01`.
    * Verify that the port is enabled, configured for the correct VLAN, and that there are no access control lists (ACLs) blocking traffic.
    * Check for any errors or misconfigurations in the port's settings.
* **If possible, perform a loopback test on the switch port.**
    * This can help determine if the switch port itself is functioning correctly.
    * Configure the port for loopback testing and use the switch's diagnostic tools to perform the test.
* **If spanning-tree is enabled, examine spanning-tree logs and configurations.**
    * Spanning-tree issues can sometimes cause connectivity problems.
    * Check for any spanning-tree events or errors in the switch logs.
    * Verify that the spanning-tree configuration is correct.
* **Check for duplex mismatches.**
    * Ensure the switch port and the server NIC are configured for the same duplex (full or half).
    * Duplex mismatches can cause severe performance issues.
* **Examine the switch port interface counters.**
    * Use the `show interface counters errors` command to check for interface errors.
    * Look for CRC errors, runts, giants, and other error types.
    * These errors can indicate physical layer problems or cable issues.
* **If possible, perform a packet capture on the switch port.**
    * This can provide detailed information about network traffic.
    * Use a packet analyzer (e.g., Wireshark) to analyze the captured traffic.
    * Look for ARP requests/replies, TCP/UDP traffic, and error messages.
* **If possible, check the server's operating system logs for network errors.**
    * **If `webserver01` is accessible via console or remote access, check system logs.**
        * **Linux/Unix-like Systems:**
            * Examine `/var/log/syslog`, `/var/log/messages`, and `/var/log/dmesg` for kernel and system messages.
            * Check `/var/log/auth.log` or `/var/log/secure` for authentication-related issues.
            * Review application-specific logs (e.g., web server logs).
            * Use `journalctl` to view systemd journal logs (if applicable).
        * **Windows Systems:**
            * Open Event Viewer (eventvwr.msc).
            * Check the System and Application event logs.
            * Look for network-related errors, warnings, and informational events.
            * Examine security logs for authentication failures.
    * **Look for network interface errors, firewall logs, and system events related to network connectivity.**
        * Search for keywords like "network," "interface," "eth0," "NIC," "IP," "ARP," "firewall," "route," and "DNS."
        * Look for error messages related to link state changes, IP address conflicts, ARP failures, routing issues, and firewall blocks.
    * **Save relevant log excerpts to a file (e.g., `webserver01-system-logs.txt`).**
    * **If `webserver01` is not accessible:**
        * Document that the server logs could not be checked, due to the server being unreachable.
        * Indicate that reviewing the server logs should be a priority once connectivity is restored.
* **Review the server NIC configuration.**
    * Ensure that the NIC is enabled and configured correctly.
    * If possible, check the NIC's advanced settings for duplex, speed, and flow control.
* **Review the server firewall configuration.**
    * Verify that the firewall is not blocking necessary traffic.
    * Check for any recent firewall rule changes.

**Extremely Detailed Next Steps:**

1.  **DNS Checks (Detailed):**
    * **Command (Windows):** `nslookup webserver01`
        * **Expected Output (If Working):**
            ```
            Server:  [DNS Server IP]
            Address: [DNS Server IP]

            Name:    [webserver01.example.com](https://www.google.com/search?q=webserver01.example.com)
            Address: 192.168.1.10
            ```
        * **Command (Linux):** `dig webserver01`
        * **Analyze:** Look for:
            * "SERVFAIL" or "NXDOMAIN" responses.
            * Incorrect IP addresses.
            * DNS server timeouts.
        * **Action:** If DNS is failing, check DNS server logs and connectivity.

2.  **Access Switch Log Analysis (Extremely Detailed):**
    * **Command (Cisco):** `show logging | include GigabitEthernet1/0/1`
        * **Analyze:** Look for:
            * "line protocol up/down" messages.
            * "errdisable" events.
            * "bpduguard" or "rootguard" messages.
            * "MAC address flapping" messages.
        * **Command (Juniper):** `show log messages | match ge-0/0/1`
        * **Action:** Address any errors found (e.g., enable port, disable errdisable, fix STP issues).

3.  **ARP and MAC Address Table Checks (Extremely Detailed):**
    * **Command (Cisco):** `show arp | include 192.168.1.10`
        * **Expected Output (If Working):**
            ```
            Internet  192.168.1.10             XX:XX:XX:XX:XX:XX  ARPA   GigabitEthernet1/0/1
            ```
    * **Command (Cisco):** `show mac address-table address XX:XX:XX:XX:XX:XX`
    * **Command (Juniper):** `show arp | match 192.168.1.10`
    * **Analyze:**
        * Missing ARP entry means Layer 2 communication is failing.
        * Incorrect MAC address means a configuration issue.
        * Incorrect interface means a wiring or VLAN problem.
    * **Action:** Correct ARP/MAC address table issues.

4.  **Switch Port Configuration Examination (Extremely Detailed):**
    * **Command (Cisco):** `show interface GigabitEthernet1/0/1`
        * **Analyze:** Check for:
            * "Status" (should be "connected").
            * "Duplex" (should match server NIC).
            * "Speed" (should match server NIC).
            * "VLAN" (should be correct).
            * "Input/Output errors."
    * **Command (Juniper):** `show interfaces ge-0/0/1`
    * **Action:** Correct any configuration mismatches.

5.  **Loopback Test (Extremely Detailed):**
    * **Configuration (Cisco):**
        ```
        interface GigabitEthernet1/0/1
        loopback
        ```
    * **Test:** Ping the switch's IP address from a connected device.
    * **Action:** If the test fails, the port is faulty.

6.  **Spanning-Tree Checks (Extremely Detailed):**
    * **Command (Cisco):** `show spanning-tree interface GigabitEthernet1/0/1`
        * **Analyze:** Check for:
            * "Port Status" (should be "FWD" or "DESG").
            * "PortFast" (should be enabled for server ports).
            * "BPDU Guard" (should be disabled for server ports unless specifically needed).
        * **Command (Juniper):** `show spanning-tree interface ge-0/0/1`
    * **Action:** Correct any STP issues.

7.  **Duplex Mismatch Check (Extremely Detailed):**
    * **Command (Cisco):** `show interface GigabitEthernet1/0/1`
        * **Analyze:** Look for "Duplex: full" or "Duplex: half."
    * **Server NIC (Windows):** Device Manager -> Network Adapters -> [NIC] -> Properties -> Advanced -> Speed & Duplex.
    * **Server NIC (Linux):** `ethtool eth0`
    * **Action:** Ensure both sides match.

8.  **Interface Counter Checks (Extremely Detailed):**
    * **Command (Cisco):** `show interface GigabitEthernet1/0/1 counters errors`
        * **Analyze:** Look for:
            * "CRC errors" (indicates cable issues).
            * "Runts" (indicates collisions or cable issues).
            * "Giants" (indicates MTU mismatches).
        * **Command (Juniper):** `show interfaces ge-0/0/1 extensive | match errors`
    * **Action:** Replace faulty cables or adjust MTU settings.

9.  **Packet Capture (Extremely Detailed):**
    * **Configuration (Cisco):**
        ```
        monitor session 1 source interface GigabitEthernet1/0/1
        monitor session 1 destination interface GigabitEthernet1/0/2
        ```
    * **Wireshark:** Filter for `arp`, `icmp`, `tcp.port == 80`, etc.
    * **Analyze:** Look for:
        * ARP requests without replies.
        * ICMP request timeouts.
        * TCP retransmissions.
        * Malformed packets.
    * **Action:** Identify and correct protocol-level issues.

10. **Server OS Log Analysis (Extremely Detailed):**
    * **Linux:** `grep -i "eth0\|network\|error" /var/log/syslog`
    * **Windows:** Event Viewer -> System -> Filter Current Log -> Keywords: Network, Error
    * **Analyze:**
        * NIC driver errors.
        * Firewall rule blocks.
        * Routing table issues.
        * Authentication failures.
    * **Action:** Correct OS-level issues.

11. **Server NIC Configuration Review (Extremely Detailed):**
    * **Linux:** `ethtool eth0`, `ip addr show eth0`, `route -n`
    * **Windows:** `ipconfig /all`, Device Manager -> [NIC] -> Properties
    * **Analyze:**
        * IP address conflicts.
        * Incorrect gateway.
        * Disabled NIC.
        * Outdated drivers.
    * **Action:** Correct NIC configuration.

12. **Server Firewall Configuration Review (Extremely Detailed):**
    * **Linux:** `iptables -L`, `ufw status`
    * **Windows:** Windows Defender Firewall with Advanced Security
    * **Analyze:**
        * Blocked ports.
        * Incorrect rules.
        * Disabled firewall.
    * **Action:** Correct firewall rules.
