# Solution Implementation

**Date:** 2024-10-27
**Time:** 16:45 PST

**Subject:** Resolution of Network Outage Affecting `webserver01` (192.168.1.10)

**1. Problem Summary:**

`webserver01` became unreachable due to a port security violation on the access switch (GigabitEthernet1/0/1) and a missing DNS "A" record.

**2. Solution Steps:**

* **2.1 Correcting Port Security Violation:**
    * Connected to the access switch (Rack A Switch) via SSH.
    * Executed the command `show port-security interface GigabitEthernet1/0/1` to examine the current port security configuration.
    * Identified the issue: The MAC address of `webserver01` was not listed as a secure MAC address.
    * Entered global configuration mode: `configure terminal`.
    * Entered interface configuration mode: `interface GigabitEthernet1/0/1`.
    * Added the correct MAC address of `webserver01`: `switchport port-security mac-address [webserver01_MAC_address]`.
    * Alternatively, if the maximum mac addresses were exceeded, the command `switchport port-security maximum [number]` would have been used.
    * Re-enabled the port using `shutdown` followed by `no shutdown`.
    * Verified the port status using `show interfaces status`.
* **2.2 Adding DNS "A" Record:**
    * Logged into the DNS server management interface (Windows DNS Manager).
    * Navigated to the forward lookup zone for `example.com`.
    * Created a new "A" record with the following details:
        * Name: `webserver01`
        * IP Address: `192.168.1.10`
    * Saved the changes.
    * Verified that the DNS record had propagated.
* **2.3 Verification:**
    * Performed a ping test from a local workstation to `192.168.1.10`. Successful.
    * Performed a ping test from the core switch to `192.168.1.10`. Successful.
    * Performed a traceroute test from a local workstation to `192.168.1.10`. Successful.
    * Executed `nslookup webserver01` from a local workstation. Verified the correct IP address (192.168.1.10) was returned.
    * Attempted to access the website hosted on `webserver01` from a web browser. Successful.
    * Checked the access switch logs using `show logging`. Confirmed no new port security violations.
    * If accessible, confirmed that the webserver was logging connections.

**3. Results:**

* Network connectivity to `webserver01` was restored.
* DNS resolution for `webserver01` was restored.
* The website hosted on `webserver01` is now accessible.

**4. Post-Solution Actions:**

* Documented the incident and its resolution in an incident report.
* Updated network diagrams and configuration documentation with the correct MAC address and port security configuration.
* Reviewed and updated security policies related to port security.
* Communicated the resolution to affected users and stakeholders.
* Scheduled a post-incident review meeting to discuss preventative measures.
* Implemented monitoring on the switch port to ensure that another security violation does not occur.

**5. Conclusion:**

The network outage affecting `webserver01` was successfully resolved by correcting the port security violation and adding the missing DNS "A" record. Post-solution actions were taken to prevent recurrence and improve network management practices.
