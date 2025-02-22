# Network Outage Scenario: Web Server Inaccessibility

**Date:** 2024-10-27
**Time:** 14:30 PST

**Description:**

A critical web server, `webserver01` (IP address: 192.168.1.10), located in rack A, suddenly became inaccessible. Users attempting to access the company's public-facing website, hosted on `webserver01`, reported "connection timed out" errors. This outage caused significant disruption to external customer access.

**Symptoms:**

* **Website Inaccessibility:** Users attempting to access the website received "connection timed out" errors in their web browsers.
* **No Ping Response:** Attempts to ping `webserver01` from various locations within the data center network resulted in 100% packet loss.
* **High Latency (Initial Reports):** Before the complete outage, some users reported slow loading times and intermittent connectivity issues.
* **DNS Resolution Failure (Observed later):** Attempts to resolve `webserver01`'s hostname via `nslookup` failed.

**Affected Systems:**

* `webserver01` (192.168.1.10)
* All external users attempting to access the company website.
* Potentially, internal applications that rely on `webserver01`.

**Network Topology (Relevant Portion):**

[Internet] --> [Firewall] --> [Core Switch] --> [Access Switch] --> webserver01

**Initial Observations:**

* The issue was reported by multiple users simultaneously, indicating a widespread problem.
* No recent maintenance or configuration changes were scheduled for `webserver01` or any related network devices.
* Network monitoring tools indicated a sudden drop in network traffic to `webserver01`.

**Objective:**

To diagnose and resolve the network outage affecting `webserver01`, restore website access, and document the troubleshooting process, root cause, and solution.
