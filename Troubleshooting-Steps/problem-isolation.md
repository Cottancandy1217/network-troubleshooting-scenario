# Problem Isolation

**Date:** 2024-10-27
**Time:** 16:00 PST

**Objective:** To systematically isolate the root cause of the network outage affecting `webserver01` (192.168.1.10) by eliminating potential points of failure.

**1. Physical Layer Isolation:**

* **Initial Observation:** No link lights on `webserver01`'s NIC and the corresponding switch port.
* **Cable Integrity:** Visual inspection and cable testing (if available) indicated a potential issue with the cable.
* **Cable Reseating:** Reseating the cable did not resolve the issue.
* **Known Good Cable:** Replacing the cable with a known good cable did not resolve the issue.
* **Conclusion:** The physical layer issue is not solely a cable problem.

**2. Switch Port Isolation:**

* **Test Laptop Connection:** Connecting a test laptop to the same switch port resulted in successful network connectivity.
* **Different Switch Port:** Connecting `webserver01` to a different switch port did not resolve the issue.
* **Different Server on Same Port:** Connecting a different server to the original switch port resulted in the same connectivity issues.
* **Switch Port Configuration:** Upon examination, the switch port was found to be administratively disabled (shutdown) due to a port security violation.
* **Conclusion:** The switch port itself was functional, but administratively disabled due to a security violation.

**3. Server NIC Isolation:**

* **NIC Link Lights:** The server NIC's link lights remained off during the outage.
* **Server Access:** `webserver01` was inaccessible, preventing direct NIC configuration checks.
* **Switch Port Resolution:** After fixing the switch port issue, the server NIC began functioning.
* **Conclusion:** The server NIC was likely working correctly, but unable to establish a link due to the switch port being disabled.

**4. Network Layer Isolation:**

* **Ping Tests:** `webserver01` was unreachable from local workstations, the core switch, and the firewall.
* **Traceroute Tests:** Traceroute tests failed to reach `webserver01`, stopping at the default gateway.
* **IP Configuration:** IP configurations of the local workstation and test laptop were valid.
* **ARP and MAC Address Tables:** The MAC address of `webserver01` was not present in the switch's MAC address table when the port was down.
* **Conclusion:** The network layer connectivity was failing due to the layer 2 issue with the switch port being disabled.

**5. DNS Isolation:**

* **NSLookup Tests:** DNS resolution for `webserver01` failed from all tested locations.
* **Authoritative DNS Server:** Checking the authoritative DNS server revealed a missing A record for `webserver01`.
* **Conclusion:** The DNS resolution failure was due to the missing A record.

**6. Log Analysis Isolation:**

* **Access Switch Logs:** Logs confirmed the port security violation and the port entering an error-disabled state.
* **Core Switch Logs:** Logs showed the removal of `webserver01`'s MAC address from the MAC address table.
* **Firewall Logs:** Logs showed denied traffic, as expected.
* **Server Logs (If Available):** Logs showed the server's network interface going down.
* **Conclusion:** The logs corroborated the findings from other isolation steps, pointing to the port security violation as the root cause of the immediate outage.

**Root Cause:**

* The primary root cause of the network outage was a port security violation on the access switch port connected to `webserver01`, which resulted in the port being administratively disabled.
* A secondary issue was the missing A record in DNS, which caused DNS resolution to fail.

**Next Steps:**

* Correct the port security violation and re-enable the port.
* Add the missing A record to the DNS server.
* Verify network connectivity and DNS resolution for `webserver01`.
* Review and update security policies related to port security.
* Document the incident and its resolution.

**Detailed Next Steps:**

1.  **Correct the Port Security Violation and Re-enable the Port:**
    * **Access the Access Switch CLI:** Connect to the access switch via console or SSH.
    * **Check Port Security Configuration:**
        * Use `show port-security interface GigabitEthernet1/0/1` (or the relevant interface).
        * Note the `Secure MAC Address(es)` and the `Maximum MAC Addresses`.
    * **Identify the Violation:**
        * If the MAC address of `webserver01` is not listed, add it using:
            * `configure terminal`
            * `interface GigabitEthernet1/0/1`
            * `switchport port-security mac-address [webserver01's MAC address]`
        * If the maximum allowed MAC addresses were exceeded, increase the limit using:
            * `switchport port-security maximum [new maximum number]`
    * **Re-enable the Port:**
        * If the port is in an error-disabled state, use:
            * `errdisable recovery cause psecure-violation` (if configured)
        * Alternatively, use:
            * `shutdown`
            * `no shutdown`
    * **Verify Port Status:** Use `show interfaces status` to confirm the port is up.

2.  **Add the Missing A Record to the DNS Server:**
    * **Access DNS Management Console:** Log in to the DNS server's management interface (e.g., Windows DNS Manager, BIND configuration files).
    * **Navigate to the Forward Lookup Zone:** Find the `example.com` zone.
    * **Create the A Record:**
        * Add a new "A" record with the following details:
            * Name: `webserver01`
            * IP Address: `192.168.1.10`
        * Save the changes.
    * **Increment Serial Number (if applicable):** If using BIND, increment the SOA record's serial number.
    * **Reload the Zone:** Reload the `example.com` zone on the DNS server.
    * **Verify Replication:** If using multiple DNS servers, ensure the changes replicate.

3.  **Verify Network Connectivity and DNS Resolution for `webserver01`:**
    * **Ping Test:**
        * Ping `192.168.1.10` from a local workstation and the core switch.
    * **Traceroute Test:**
        * Perform a traceroute to `192.168.1.10` from a local workstation.
    * **NSLookup Test:**
        * Run `nslookup webserver01` from a local workstation. Verify the correct IP address is returned.
    * **Website Access Test:**
        * Attempt to access the website hosted on `webserver01` from a web browser.
    * **Check Switch Logs:**
        * Use `show logging` on the access switch to confirm no new port security violations.
    * **Check Server Logs (if accessible):**
        * Verify network connectivity has been restored.

4.  **Review and Update Security Policies Related to Port Security:**
    * **Analyze the Incident:** Determine why the port security violation occurred.
    * **Review Existing Policies:** Examine the current port security policies.
    * **Update Policies:**
        * If the violation was due to an incorrect MAC address, update the documentation and procedures for adding or changing MAC addresses.
        * If the violation was due to an unauthorized device, strengthen access control measures.
        * If the violation was due to a device replacement, create a clear procedure for updating the mac address.
    * **Implement 802.1X (if applicable):** Consider implementing 802.1X authentication for stronger port security.
    * **Document Changes:** Record all changes made to the security policies.
    * **Communicate Changes:** Inform relevant personnel about the updated policies.

5.  **Document the Incident and Its Resolution:**
    * **Create an Incident Report:**
        * Include the timeline of events.
        * Summarize the symptoms and troubleshooting steps.
        * Document the root cause (port security violation and missing DNS record).
        * Describe the resolution steps.
        * Include relevant log excerpts and command outputs.
        * Note any lessons learned and recommendations.
    * **Update Network Documentation:**
        * Update network diagrams and configuration documentation.
        * Record the correct MAC address of `webserver01`.
        * Document the port security configuration.
    * **Communicate with Stakeholders:**
        * Inform affected users and stakeholders about the resolution.
        * Share the incident report if necessary.
    * **Monitor for Stability:**
        * Monitor `webserver01` and the access switch port for a period of time to ensure stability.
        * Check for any recurring errors or performance issues.

**Further Steps and Considerations:**

6.  **Proactive Security Measures:**
    * **Implement Intrusion Detection/Prevention Systems (IDS/IPS):** Enhance security by deploying IDS/IPS to detect and prevent unauthorized access.
    * **Regular Vulnerability Scanning:** Schedule regular vulnerability scans of network devices and servers to identify and address potential security weaknesses.
    * **Security Information and Event Management (SIEM):** Implement SIEM to consolidate and analyze logs from various sources, enabling proactive threat detection.
    * **Network Access Control (NAC):** Consider NAC solutions to enforce security policies before granting network access to devices.
7.  **Automation and Monitoring:**
    * **Automate Port Security Configuration:** Implement scripts or tools to automate the configuration of port security on switch ports.
    * **Implement Network Monitoring Tools:** Use network monitoring tools to proactively detect network issues and security violations.
    * **Set up Alerts:** Configure alerts for critical events, such as port security violations, interface down events, and DNS errors.
8.  **Training and Awareness:**
    * **Conduct Regular Security Training:** Provide regular security training to IT staff and users to raise awareness of security threats and best practices.
    * **Develop a Knowledge Base:** Create a knowledge base of common troubleshooting procedures and security best practices.
9.  **Disaster Recovery and Business Continuity:**
    * **Review and Update Disaster Recovery Plans:** Ensure that disaster recovery plans are up-to-date and include procedures for restoring network connectivity and DNS services.
    * **Implement Redundancy:** Implement redundant network devices and DNS servers to minimize downtime in case of failures.
    * **Regular Backups:** Implement regular backups of critical systems and configurations.
10. **Post-Incident Review Meeting:**
    * **Conduct a Post-Incident Review Meeting:** Gather all involved personnel to discuss the incident, identify lessons learned, and develop recommendations for improvement.
    * **Document Recommendations:** Document all recommendations from the post-incident review meeting and assign responsibilities for implementation.
