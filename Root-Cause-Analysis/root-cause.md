# Root Cause Analysis

**Date:** 2024-10-27
**Time:** 16:30 PST

**Subject:** Network Outage Affecting `webserver01` (192.168.1.10)

**1. Summary of Incident:**

At approximately 14:30 PST, `webserver01` became unreachable. Users reported website access failures, and network monitoring tools indicated a loss of connectivity to the server.

**2. Symptoms:**

* No link lights on `webserver01`'s NIC and the corresponding switch port (GigabitEthernet1/0/1).
* Inability to ping `webserver01` from local workstations, core switch, and firewall.
* Traceroute tests failed to reach `webserver01`.
* DNS resolution for `webserver01` failed from all tested locations.
* Access switch logs showed port GigabitEthernet1/0/1 in an err-disabled state due to a port security violation.
* Core switch logs showed the MAC address of `webserver01` being removed from the MAC address table.
* Firewall logs showed traffic destined for `webserver01` being denied.
* (If accessible) `webserver01` system logs showed the network interface going down.

**3. Timeline of Events:**

* **14:30:10 PST:** Switch port GigabitEthernet1/0/1 experiences link state changes.
* **14:30:14 PST:** MAC address of `webserver01` moves from Gi1/0/1 to Gi1/0/2, then shortly after is removed from the mac address table.
* **14:30:15 PST:** GigabitEthernet1/0/1 link status goes down.
* **14:30:16 PST:** GigabitEthernet1/0/1 is placed in err-disable state due to a port security violation.
* **14:30:15 PST (approx):** Firewall starts denying traffic destined for `webserver01`.
* **14:30:14 PST (if accessible):** `webserver01`'s system logs show the network interface going down.
* **15:30 PST:** DNS resolution for `webserver01` is confirmed to be failing.
* **16:00 PST:** Problem isolation confirms port security violation and missing DNS A record.
* **16:15 PST:** Port security violation corrected, and port re-enabled.
* **16:20 PST:** Missing DNS A record added.
* **16:25 PST:** Network connectivity and DNS resolution verified.

**4. Root Cause Analysis:**

* **Primary Root Cause:**
    * The primary root cause of the network outage was a **port security violation** on the access switch port (GigabitEthernet1/0/1) connected to `webserver01`. This violation resulted in the switch port being placed in an error-disabled state, effectively shutting down the connection.
    * The port security violation was due to the MAC address of the device connected to the port not matching the configured secure MAC address, or an unauthorized device connecting to the port.
* **Secondary Root Cause:**
    * A secondary root cause was the **missing DNS "A" record** for `webserver01` on the authoritative DNS server. This prevented successful DNS resolution, compounding the connectivity issues.

**5. Contributing Factors:**

* Potentially, a lack of up-to-date documentation or procedures for managing port security configurations.
* Potentially, a lack of proper change control regarding device replacement.
* Potentially, a lack of regular DNS record verification.

**6. Corrective Actions:**

* Corrected the port security violation by adding the correct MAC address or adjusting the maximum MAC address count on the affected switch port.
* Re-enabled the switch port.
* Added the missing "A" record for `webserver01` to the DNS server.
* Verified network connectivity and DNS resolution.
* Reviewed and updated security policies related to port security.
* Documented the incident and its resolution.

**7. Preventative Measures:**

* Implement regular audits of port security configurations.
* Strengthen change management procedures for device replacements.
* Implement regular DNS record verification.
* Implement stronger network access control mechanisms, such as 802.1X authentication.
* Provide security awareness training to IT staff and users.
* Implement a SIEM solution to consolidate log information.
* Implement network monitoring tools to alert on port status changes.

**8. Conclusion:**

The network outage affecting `webserver01` was primarily caused by a port security violation. Prompt troubleshooting and corrective actions restored network connectivity. Preventative measures will be implemented to minimize the risk of similar incidents in the future.
