# Ping Tests

**Date:** 2024-10-27
**Time:** 14:50 PST

**Objective:** To test network connectivity to `webserver01` (192.168.1.10) from various network locations.

**Test 1: Ping from a Local Workstation (Same Subnet)**

* **Command:** `ping 192.168.1.10`
* **Output:**
    ```
    Pinging 192.168.1.10 with 32 bytes of data:
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 192.168.1.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```
* **Interpretation:** No connectivity to `webserver01` from a local workstation.

**Test 2: Ping from a Different Subnet (Core Switch)**

* **Command:** `ping 192.168.1.10` (Executed from the core switch's CLI)
* **Output:**
    ```
    % Network is unreachable.
    ```
* **Interpretation:** The core switch cannot reach `webserver01`, indicating a potential layer 2 issue or a problem with the access switch.

**Test 3: Ping from the Firewall (External Network Simulation)**

* **Command:** `ping 192.168.1.10` (Executed from the firewall's CLI)
* **Output:**
    ```
    % Destination host unreachable.
    ```
* **Interpretation:** The firewall cannot reach `webserver01`, confirming the server is unreachable from outside the local network.

**Test 4: Ping from Test Laptop (Directly Connected to Switch Port)**

* **Command:** `ping 192.168.1.10` (Executed from a test laptop directly connected to the switch port previously used by `webserver01`.)
* **Output:**
    ```
    Pinging 192.168.1.10 with 32 bytes of data:
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 192.168.1.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```
* **Interpretation:** Even with a direct connection to the switch port, the test laptop cannot reach `webserver01`. This further isolates the issue to a problem between the switch port and the server.

**Findings:**

* `webserver01` is unreachable from all tested network locations.
* The issue is not limited to a single subnet.
* The issue is likely located between the switch port and the server itself.
* The fact that the test laptop also failed to ping the server while directly connected to the switch port indicates the issue is likely not a routing issue upstream, but rather an issue with the port itself or the server.

**Next Steps:**

* **Proceed with `traceroute` tests to trace the network path and identify the point of failure.**
    * Perform `traceroute` (or `tracert` on Windows) from a local workstation to `webserver01`.
        * Document the output of the `traceroute/tracert` command.
        * Analyze the output to see where the path fails.
    * Perform `traceroute` (or `tracert` on Windows) from the core switch to `webserver01`.
        * Document the output of the `traceroute/tracert` command.
        * Analyze the output to see where the path fails.
    * Perform `traceroute` (or `tracert` on Windows) from the firewall to `webserver01`.
        * Document the output of the `traceroute/tracert` command.
        * Analyze the output to see where the path fails.
* **Continue with log analysis to gather more information.**
    * Examine the logs of the access switch connected to `webserver01` for any errors or warnings.
        * Save relevant log excerpts.
        * Look for port status changes, errors, or warnings related to the port `webserver01` is connected to.
    * Check the logs of the core switch and firewall for any relevant entries.
        * Save relevant log excerpts.
        * Look for routing issues, firewall blocks, or other related errors.
    * Review the operating system logs on `webserver01` (if accessible) for network-related errors.
        * If the server is accessible via console or another means, examine system logs for NIC errors or other network related issues.
    * Review any logs available from the test laptop that was directly connected to the switch port.
        * Check the test laptop's system logs for any network related messages.
        * If the test laptop has packet capture software, review any captured packets.
* **Further Problem Isolation:**
    * If a spare switch port is available, try connecting `webserver01` to that port.
        * Document the result of this test.
    * If a spare network cable is available, try replacing the existing cable.
        * Document the result of this test.
    * If possible, try connecting a different known-good server to the switch port.
        * Document the result of this test.
    * Examine the access switch port configuration for any errors or misconfigurations.
        * Save the port configuration.
    * Examine the `webserver01` NIC configuration (if possible) for any errors or misconfigurations.
        * Save the NIC configuration.
    * Examine the `webserver01` firewall configuration (if possible) for any errors or misconfigurations.
        * Save the firewall configuration.
    * **Check the VLAN configuration on the access switch.**
        * Ensure the port is assigned to the correct VLAN.
        * Document the VLAN configuration for the port.
    * **Verify the MAC address table on the access switch.**
        * Check if the MAC address of `webserver01`'s NIC is present in the table.
        * Document the relevant entries from the MAC address table.
    * **Check the ARP table on the access switch.**
        * Verify if the IP address and MAC address of `webserver01` are correctly associated.
        * Document the relevant entries from the ARP table.
    * **Check for any spanning-tree issues on the access switch.**
        * Ensure the port is in a forwarding state.
        * Document the spanning-tree status of the port.
    * **Check for any port security configurations on the access switch.**
        * Verify if there are any MAC address restrictions on the port.
        * Document the port security configuration.
    * **Check for any access control lists (ACLs) on the access switch.**
        * Verify if there are any ACLs that might be blocking traffic to `webserver01`.
        * Document any relevant ACL configurations.
