# Physical Connection Checks

**Date:** 2024-10-27
**Time:** 14:45 PST

**Objective:** To verify the physical integrity of network connections related to `webserver01`.

**Steps Taken:**

1.  **Visual Inspection of `webserver01` NIC:**
    * Inspected the network interface card (NIC) on `webserver01`.
    * Observed that the link lights on the NIC were not illuminated.
    * Documented: "NIC link lights on `webserver01` are off."

2.  **Visual Inspection of Switch Port:**
    * Inspected the switch port connected to `webserver01` on the access switch in Rack A.
    * Observed that the link lights on the switch port were also not illuminated.
    * Documented: "Switch port link lights are off."

3.  **Cable Integrity Check:**
    * Visually inspected the network cable connecting `webserver01` to the switch.
    * Checked for any visible damage, kinks, or cuts.
    * Found no visible signs of damage.
    * Documented: "Network cable appears to be physically intact."

4.  **Cable Reseating:**
    * Reseated the network cable at both the `webserver01` NIC and the switch port.
    * Ensured the cable was firmly plugged in.
    * Observed no change in the link lights.
    * Documented: "Reseated cable, link lights remain off."

5.  **Cable Tester (If Available):**
    * Connected a network cable tester to the cable.
    * The cable tester indicated a continuity issue (or a specific fault depending on the tester).
    * Documented: "Cable tester indicates [specify the fault, e.g., open circuit, short circuit]."
    * (If a cable tester was not available, document that you attempted to use one and that one was not available.)

6.  **Photo Documentation:**
    * Took photos of the `webserver01` NIC, the switch port, and the cable connection.
    * Saved the photos as:
        * `physical-checks-nic.jpg`
        * `physical-checks-switchport.jpg`
        * `physical-checks-cable.jpg`

**Findings:**

* The lack of link lights on both the server NIC and the switch port strongly suggests a physical layer issue.
* The cable tester results (if available) confirm a problem with the network cable.
* Reseating the cable did not resolve the issue.

**Next Steps:**

* **Proceed with network diagnostic tools to further isolate the problem.**
    * Conduct `ping` tests from various network locations to `webserver01`.
    * Perform `traceroute` (or `tracert` on Windows) to trace the network path and identify any potential bottlenecks or points of failure.
    * Use `ipconfig` (Windows) or `ifconfig` (Linux) on a local machine to verify IP configuration.
    * Use `nslookup` to check DNS resolution for `webserver01`.
* **Analyze network logs to identify any potential errors.**
    * Examine the switch logs for the port connected to `webserver01`.
    * Review the logs of the core switch and firewall for any relevant entries.
    * Check the server's operating system logs (if accessible) for network-related errors.
* **Isolate the Problem:**
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
        * Examined the switch port configuration using the switch's CLI.
        * Found that the port was administratively disabled (shutdown).
        * Documented the switch port configuration: "Switch port configured as administratively down."
    * **Server NIC Configuration:**
        * Since the switch port was down, the server NIC configuration was not likely the culprit.
        * Documented: "Server NIC configuration not examined due to switch port being down."
    * **Server Firewall Configuration:**
        * Since the server was not reachable, the firewall configuration was not likely the culprit.
        * Documented: "Server firewall configuration was not examined due to the server not being reachable. However, the firewall configuration should be reviewed after network connectivity is restored to ensure it does not block necessary traffic."
