# Network Troubleshooting Scenario

This project documents a simulated network outage scenario and the troubleshooting process, demonstrating problem-solving skills in a data center environment.

## Scenario

**Description:** A critical web server, `webserver01`, located in rack A, suddenly became inaccessible. Users reported "connection timed out" errors when attempting to access the website. The issue was reported at 14:30 on 2024-10-27.

## Author
Angelee Morquin

## Contact

* Email: sweety.angel1217@gmail.com
* LinkedIn: https://ph.linkedin.com/in/angelee-morquin-934b51125/

**Symptoms:**

* Website inaccessible.
* "Connection timed out" errors.
* Possible network latency.

**Affected Systems:**

* `webserver01` (IP: 192.168.1.10)
* Users attempting to access the website.

## Troubleshooting Methodology

1.  **Gather Information:** Verify the reported issue and review recent network changes.
2.  **Check Physical Connections:** Verify physical cables and connections on `webserver01` and network switches.
3.  **Use Network Diagnostic Tools:** Use `ping`, `traceroute`, `ipconfig`, and `nslookup` to diagnose network connectivity.
4.  **Analyze Network Logs:** Review system and network device logs for errors.
5.  **Isolate the Problem:** Narrow down the root cause by testing network components.

## Troubleshooting Steps

### 1. Gather Information

* Verified the reported issue by attempting to access the website from multiple locations.
* Reviewed recent network change logs and found no relevant changes.
* Checked network monitoring tools and observed high latency for `webserver01`.
    * Screenshot of monitoring tool: `![Monitoring Tool Screenshot](Troubleshooting-Steps/monitoring-tool.png)`

### 2. Check Physical Connections

* Verified network cable connection on `webserver01` and the corresponding switch port.
* Checked for any loose connections or physical damage.
* All connections appeared to be secure.
    * Photo of cabling: `![Cabling Photo](Troubleshooting-Steps/cabling.jpg)`

### 3. Use Network Diagnostic Tools

* **Ping Test:**
    * Command: `ping 192.168.1.10`
    * Output:
        ```
        Pinging 192.168.1.10 with 32 bytes of data:
        Request timed out.
        Request timed out.
        Request timed out.
        Request timed out.

        Ping statistics for 192.168.1.10:
            Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
        ```
    * Interpretation: No connectivity to `webserver01`.
* **Traceroute Test:**
    * Command: `tracert 192.168.1.10`
    * Output:
        ```
        Tracing route to 192.168.1.10 over a maximum of 30 hops

          1    <1 ms    <1 ms    <1 ms  192.168.1.1
          2     * * * Request timed out.
          3     * * * Request timed out.
        ```
    * Interpretation: Traffic is not reaching `webserver01`.
* **IPConfig Test:**
    * Command: `ipconfig /all`
    * Output: (Relevant output only)
        ```
        Ethernet adapter Ethernet:

           IPv4 Address. . . . . . . . . . . : 192.168.1.100
           Subnet Mask . . . . . . . . . . . : 255.255.255.0
           Default Gateway . . . . . . . . . : 192.168.1.1
        ```
    * Interpretation: IP settings on the test client are correct.
* **NSLookup Test:**
    * Command: `nslookup webserver01`
    * Output:
        ```
        Server:  UnKnown
        Address:  192.168.1.1

        *** UnKnown can't find webserver01: Non-existent domain
        ```
    * Interpretation: DNS resolution for `webserver01` failed.

### 4. Analyze Network Logs

* Reviewed switch logs and found an error message indicating a port on the switch connected to `webserver01` was disabled.
    * Log snippet: `![Switch Log Snippet](Troubleshooting-Steps/switch-log.txt)`

### 5. Isolate the Problem

* Confirmed the switch port was disabled.
* Tested connectivity by enabling the port.
* Connectivity to `webserver01` was restored.

## Root Cause Analysis

**Root Cause:** The switch port connected to `webserver01` was inadvertently disabled, likely due to a misconfiguration or accidental command.

## Solution Implementation

* Enabled the switch port.
* Verified network connectivity to `webserver01` using `ping` and `traceroute`.
* Confirmed the website was accessible to users.
* Documented the incident and resolution in the network incident log.

## Lessons Learned

* Always double-check switch port configurations before making changes.
* Implement stricter access controls for network devices.
* Ensure proper monitoring is in place to detect port status changes.
