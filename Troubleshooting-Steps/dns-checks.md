# DNS Checks

**Date:** 2024-10-27
**Time:** 15:30 PST

**Objective:** To verify DNS resolution for `webserver01` (192.168.1.10) from various network locations and identify potential DNS-related issues.

**Test 1: NSLookup from a Local Workstation (Same Subnet)**

* **Command (Windows):** `nslookup webserver01`
* **Output:**
    ```
    Server:  UnKnown
    Address:  192.168.1.1

    *** UnKnown can't find webserver01: Non-existent domain
    ```
* **Interpretation:** DNS resolution for `webserver01` failed. The workstation's primary DNS server (192.168.1.1) is unable to resolve the hostname.

**Test 2: NSLookup from the Core Switch**

* **Command (Cisco):** `nslookup webserver01` (Executed from the core switch's CLI)
* **Output:**
    ```
    % Unrecognized host or address
    ```
* **Interpretation:** The core switch, which is configured to use the same DNS server, also failed to resolve the hostname.

**Test 3: NSLookup from the Firewall (External Network Simulation)**

* **Command (Firewall CLI):** `nslookup webserver01`
* **Output:**
    ```
    Server: [Firewalls DNS server]
    Address: [Firewalls DNS server]

    *** [Firewalls DNS Server] can't find webserver01: Non-existent domain
    ```
* **Interpretation:** The external DNS server (the firewall's configured DNS) also fails to resolve `webserver01`. This indicates that the issue is not limited to the local network's DNS server.

**Test 4: NSLookup using Public DNS (8.8.8.8) from Local Workstation**

* **Command (Windows):** `nslookup webserver01 8.8.8.8`
* **Output:**
    ```
    Server:  [google-public-dns-a.google.com](https://www.google.com/search?q=google-public-dns-a.google.com)
    Address:  8.8.8.8

    *** 8.8.8.8 can't find webserver01: Non-existent domain
    ```
* **Interpretation:** Even when using a public DNS server (Google's 8.8.8.8), the hostname cannot be resolved. This confirms that the issue is not with a specific DNS server but rather with the DNS record itself.

**Test 5: Dig from Local Workstation (If dig is installed)**

* **Command (Linux or Windows with dig installed):** `dig webserver01`
* **Output (Relevant Sections):**
    ```
    ;; QUESTION SECTION:
    ;webserver01.             IN      A

    ;; AUTHORITY SECTION:
    ;example.com.          [TTL]   IN      SOA     [ns1.example.com](https://www.google.com/search?q=ns1.example.com). [admin.example.com](https://www.google.com/search?q=admin.example.com). [Serial] [Refresh] [Retry] [Expire] [Minimum TTL]

    ;; SERVER: 192.168.1.1#53(192.168.1.1)
    ;; WHEN: [Timestamp]
    ;; MSG SIZE  rcvd: [Size]
    ```
* **Interpretation:**
    * The `QUESTION SECTION` shows the query for the "A" record of `webserver01`.
    * The absence of an `ANSWER SECTION` indicates that no record was found.
    * The `AUTHORITY SECTION` shows the SOA record for the `example.com` domain, confirming that the DNS server can reach the authoritative name server.

**Findings:**

* DNS resolution for `webserver01` fails from all tested locations.
* The issue is not limited to a single DNS server.
* The use of a public DNS server confirms that the hostname is not registered or is not reachable from the internet.
* This points to a possible issue with the DNS records for `webserver01` or the zone file itself.

**Next Steps:**

* Verify DNS records on the authoritative DNS server for `example.com`.
* Check the DNS zone file for `example.com` for any errors or missing records.
* If using a local DNS server, examine its logs for any errors related to the zone file or DNS queries.
* If the server is new, ensure the A record was created.
* Verify that the DNS server can communicate with the authoritative name server.

**Detailed Next Steps:**

1.  **Verify DNS Records on Authoritative DNS Server:**
    * **Access the DNS Management Console:** Log in to the control panel or server where the authoritative DNS records for `example.com` are managed. This could be a web-based interface, a server CLI, or a dedicated DNS server management tool.
    * **Check the Zone File:** Locate the zone file for `example.com`.
    * **Look for `webserver01` A Record:** Search for an "A" record associated with `webserver01`.
        * **If Found:**
            * Verify the IP address associated with the record. It should be 192.168.1.10.
            * Check the TTL (Time To Live) value.
        * **If Not Found:**
            * This confirms the missing record as the primary issue.
    * **Check for CNAME or other records**: Make sure there aren't any conflicting records.

2.  **Check DNS Zone File for Errors:**
    * **Syntax Errors:** Examine the zone file for any syntax errors, such as missing semicolons, incorrect record formats, or mismatched parentheses.
    * **Serial Number:** Verify that the zone file's serial number has been incremented after any recent changes.
    * **SOA Record:** Ensure the Start of Authority (SOA) record is correctly configured.
    * **NS Records:** Confirm that the Name Server (NS) records point to the correct authoritative name servers.

3.  **Local DNS Server Log Examination:**
    * **Locate Log Files:** Find the log files for the local DNS server (e.g., `/var/log/named.log` for BIND, Event Viewer for Windows DNS).
    * **Filter for `webserver01` Queries:** Search the logs for entries related to DNS queries for `webserver01`.
    * **Look for Error Messages:** Check for error messages such as "NXDOMAIN," "SERVFAIL," "REFUSED," or "timeout."
    * **Check Zone File Loading Errors:** Look for errors related to loading or parsing the `example.com` zone file.
    * **Check communication with upstream DNS servers**: Make sure there are no errors related to communicating with outside DNS servers.

4.  **A Record Creation Verification:**
    * **If `webserver01` is a new server:** Double-check that the "A" record was created in the DNS zone file.
    * **If using a dynamic DNS service:** Verify that the dynamic DNS client on `webserver01` is correctly configured and has successfully registered the hostname.

5.  **Authoritative Name Server Communication Verification:**
    * **Use `dig` or `nslookup` to query the authoritative name servers directly:**
        * `dig @[Authoritative NS IP] webserver01`
        * `nslookup webserver01 [Authoritative NS IP]`
    * **Check for Firewall Rules:** Verify that firewall rules on the local network and the authoritative name server's network are not blocking DNS traffic (port 53).
    * **Check network connectivity:** Make sure that the local DNS server can communicate with the authoritative name servers via ping or traceroute.
    * **Check for DNSSEC issues:** If DNSSEC is enabled, make sure that all DNSSEC related records are correct.
    * **Check for delegation issues:** If the domain is delegated to other name servers, ensure the delegation is correctly configured in the parent zone.
    * **Check for DNS recursion issues:** If the DNS server is configured for recursion, ensure that it is able to perform recursive queries.
    * **Check for DNS forwarding issues:** If the DNS server is configured to forward queries to other DNS servers, ensure that the forwarding is correctly configured.
    * **Check for DNS caching issues:** If the DNS server is caching DNS records, ensure that the cache is not corrupted.
    * **Document all findings:** Record all findings from the DNS checks, including any errors or inconsistencies found. This documentation will be valuable for further troubleshooting and for creating a post-incident report.
