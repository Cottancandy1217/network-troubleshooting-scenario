# Log Analysis

**Date:** 2024-10-27
**Time:** 15:45 PST

**Objective:** To analyze logs from relevant network devices and systems to identify potential errors or warnings related to the `webserver01` network outage.

**1. Access Switch Logs (Rack A Switch):**

* **Command (Cisco):** `show logging | include GigabitEthernet1/0/1` (Assuming `webserver01` is connected to this port)
* **Relevant Output:**
    ```
    Oct 27 14:30:15: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet1/0/1, changed state to down
    Oct 27 14:30:16: %LINK-3-UPDOWN: Interface GigabitEthernet1/0/1, changed state to down
    Oct 27 14:30:20: %PM-4-ERR_DISABLE: psecure-violation error detected on Gi1/0/1, putting Gi1/0/1 in err-disable state
    ```
* **Interpretation:**
    * The log indicates that the interface `GigabitEthernet1/0/1` went down at 14:30:15.
    * Shortly after, the port was placed into an error-disabled state due to a "psecure-violation" (port security violation).
    * This confirms that the port was administratively shutdown due to a security violation.

**2. Core Switch Logs:**

* **Command (Cisco):** `show logging | include 192.168.1.10`
* **Relevant Output:**
    ```
    Oct 27 14:30:15: %MAC_MOVE-4-MACMOVE: Mac Address XX:XX:XX:XX:XX:XX moved from Gi1/0/1 to NULL
    ```
* **Interpretation:**
    * The core switch log shows that the MAC address of `webserver01` (XX:XX:XX:XX:XX:XX) was removed from the MAC address table when the port on the access switch went down.
    * This is the expected behavior.

**3. Firewall Logs:**

* **Command (Firewall CLI):** `show log | include 192.168.1.10`
* **Relevant Output:**
    ```
    Oct 27 14:30:15: DENY: TCP 192.168.X.X:XXXX -> 192.168.1.10:80
    Oct 27 14:30:15: DENY: TCP 192.168.X.X:XXXX -> 192.168.1.10:443
    ```
* **Interpretation:**
    * The firewall logs show that traffic destined for `webserver01` (ports 80 and 443) was denied.
    * This is expected, as the server was unreachable. These logs simply show the firewall doing its job.

**4. `webserver01` System Logs (If Accessible):**

* **Command (Linux):** `grep -i "eth0\|network\|error" /var/log/syslog`
* **Relevant Output (If Accessible):**
    ```
    Oct 27 14:30:14 webserver01 kernel: [XXXXX.XXXXXX] eth0: link down
    Oct 27 14:30:14 webserver01 systemd-networkd[XXXX]: eth0: Lost carrier
    ```
* **Interpretation:**
    * If accessible, these logs confirm that the server's network interface went down at the same time as the switch port.
    * This reinforces the evidence of a physical layer or switch port issue.

**Findings:**

* The access switch logs clearly show that the port connected to `webserver01` was placed into an error-disabled state due to a port security violation.
* The MAC address of `webserver01` disappeared from the core switch's mac address table.
* The firewall logs simply confirm that traffic was blocked, as expected.
* If accessible, the webservers logs confirm a loss of network connectivity.

**Next Steps:**

* Investigate the port security violation on the access switch.
* Determine the cause of the port security violation.
* Correct the port security violation, and re-enable the port.
* Verify network connectivity to `webserver01`.

**Detailed Next Steps:**

1.  **Investigate the Port Security Violation on the Access Switch:**
    * **Access the Access Switch CLI:** Connect to the access switch via console or SSH.
    * **Check Port Security Configuration:**
        * Use the command `show port-security interface GigabitEthernet1/0/1` (or the relevant interface).
        * Examine the following:
            * `Port Security Enabled`: Verify that port security is enabled.
            * `Maximum MAC Addresses`: Check the maximum number of allowed MAC addresses.
            * `Violation Mode`: Determine the action taken upon violation (e.g., shutdown, restrict, protect).
            * `Secure MAC Address`: List the secure MAC addresses configured on the port.
    * **Check Error-Disabled Reason:**
        * Use the command `show interfaces status err-disabled`.
        * Look for the `GigabitEthernet1/0/1` interface and the reason for the error-disable state (e.g., psecure-violation).

2.  **Determine the Cause of the Port Security Violation:**
    * **MAC Address Analysis:**
        * Compare the secure MAC addresses on the port with the actual MAC address of `webserver01` (if known).
        * If the MAC address of `webserver01` is not in the secure list, this is the cause of the violation.
        * If there are more MAC addresses learned than the maximum allowed, this is also a cause of the violation.
    * **Possible Causes:**
        * **Incorrect Configuration:** The secure MAC address may be incorrectly configured or missing.
        * **MAC Address Spoofing:** A malicious device may be attempting to spoof the MAC address of `webserver01`.
        * **Device Replacement:** `webserver01` might have been replaced with a device with a different MAC address.
        * **Unauthorized Device:** An unauthorized device might have been connected to the port.

3.  **Correct the Port Security Violation and Re-enable the Port:**
    * **If Incorrect Configuration:**
        * Enter global configuration mode: `configure terminal`.
        * Enter interface configuration mode: `interface GigabitEthernet1/0/1`.
        * Remove the existing secure MAC address (if any): `no switchport port-security mac-address [MAC address]`.
        * Add the correct MAC address: `switchport port-security mac-address [Correct MAC address]`.
        * If necessary, increase the maximum allowed MAC addresses: `switchport port-security maximum [number]`.
    * **If Device Replacement:**
        * Update the secure MAC address with the new device's MAC address.
    * **If Unauthorized Device:**
        * Investigate the unauthorized device and take appropriate security measures.
    * **Re-enable the Port:**
        * Use the command `shutdown` followed by `no shutdown` in interface configuration mode, or use the command `errdisable recovery cause psecure-violation` in global configuration mode if configured.

4.  **Verify Network Connectivity to `webserver01`:**
    * **Ping Test:**
        * Ping `webserver01` (192.168.1.10) from a workstation on the same subnet.
        * Ping `webserver01` from the core switch.
    * **Traceroute Test:**
        * Perform a traceroute to `webserver01` to verify the network path.
    * **Website Access:**
        * Attempt to access the website hosted on `webserver01` from a web browser.
    * **Log Verification:**
        * Check the switch logs to confirm that the interface is up and that there are no new port security violations.
        * Verify that the webserver is logging connections.

**5. Post-Verification and Documentation:**

* **Document the Resolution:**
    * Record the exact commands used to fix the port security violation.
    * Note the correct MAC address of `webserver01`.
    * Document the cause of the port security violation.
* **Update Configuration Documentation:**
    * If the port security configuration was incorrect, update the switch configuration documentation.
    * If a device was replaced, update the asset inventory and network diagrams.
* **Create an Incident Report:**
    * Write a summary of the incident, including the timeline, symptoms, troubleshooting steps, root cause, and resolution.
    * Include relevant log excerpts and command outputs.
    * Note any lessons learned and recommendations for preventing similar incidents in the future.
* **Communicate with Stakeholders:**
    * Inform affected users and stakeholders that the issue has been resolved.
    * Share the incident report if necessary.
* **Monitor for Stability:**
    * Monitor `webserver01` and the access switch port for a period of time to ensure stability.
    * Check for any recurring errors or performance issues.
* **If needed, review security policies:**
    * If an unauthorized device was connected, or if an incorrect MAC address was used, review the security policies related to port security.
    * Consider implementing stronger security measures, such as 802.1X authentication.

**6. Policy Review and Implementation:**

* **Policy Updates and Refinement:**
    * Identify Gaps: Based on the incident and the policy review, identify any gaps or weaknesses in the existing security policies.
    * Revise Policies: Update the security policies to address the identified gaps and strengthen security measures.
    * Document Changes: Clearly document all changes made to the security policies, including the rationale behind them.
    * Seek Approval: Obtain necessary approvals for the revised policies from relevant stakeholders and management.
* **Implementation of Enhanced Security Measures:**
    * Deploy New Technologies: If needed, deploy new security technologies, such as 802.1X authentication, intrusion detection/prevention systems (IDS/IPS), or network access control (NAC) solutions.
    * Strengthen Configurations: Implement stronger security configurations on network devices, such as more restrictive port security settings, access control lists (ACLs), and password policies.
    * Improve Monitoring: Enhance network monitoring capabilities to detect and respond to security incidents more effectively.
    * Implement security information and event management (SIEM): This will help to consolidate logs, and provide a single place to review security events.
* **Security Awareness Training:**
    * Educate Users: Provide security awareness training to users on topics such as password security, phishing prevention, and the importance of following security policies.
    * Train IT Staff: Ensure that IT staff are trained on the revised security policies and the implementation of enhanced security measures.
    * Regular Refreshers: Conduct regular security awareness training to keep users and IT staff informed of the latest security threats and best practices.
* **Regular Security Audits and Assessments:**
    * Conduct Audits: Perform regular security audits to assess the effectiveness of security policies and controls.
    * Penetration Testing: Conduct penetration testing to identify vulnerabilities in the network and systems.
    * Vulnerability Scanning: Implement regular vulnerability scanning to identify and address security weaknesses.
    * Review logs regularly: Set up a schedule to review logs from all network devices, and servers.
* **Incident Response Planning:**
    * Update Incident Response Plan: Update the incident response plan to reflect the revised security policies and enhanced security measures.
    * Conduct Drills: Conduct regular incident response drills to ensure that IT staff are prepared to respond to security incidents effectively.
    * Establish Communication Protocols: Define clear communication protocols for reporting and responding to security incidents.
* **Continuous Improvement:**
    * Monitor Security Metrics: Continuously monitor security metrics to track the effectiveness of security measures.
    * Stay Informed: Stay informed of the latest security threats and best practices by attending industry conferences, reading security publications, and participating in online forums.
    * Adapt to Changes: Adapt security policies and measures as needed to address evolving security threats and changes in the network environment.
