# Splunk Dashboards — Documentation

**Author:** Karthikeyan  
**Platform:** Splunk Enterprise  
**Built:** Day 18 — May 2026  

---

## Dashboard 1 — Authentication Overview

**Purpose:** Monitor all login activity on the endpoint

### Panels

**Panel 1 — Failed Logins Over Time**
```spl
index=* EventCode=4625
| timechart count as "Failed Logins"
```
Shows failed login trend over time. Spikes indicate brute force attempts.

**Panel 2 — Successful Logins Over Time**
```spl
index=* EventCode=4624
| timechart count as "Successful Logins"
```
Baseline for normal authentication activity.

**Panel 3 — Top Targeted Accounts**
```spl
index=* EventCode=4625
| top Account_Name
```
Shows which accounts are being targeted most.

**Panel 4 — Host Activity**
```spl
index=* EventCode=4624 OR EventCode=4625
| stats count by host
```
Shows which hosts have the most authentication activity.

---

## Dashboard 2 — Network Overview

**Purpose:** Monitor network traffic and IP activity

### Panels

**Panel 1 — Top Source IPs**
```spl
index=* | top src_ip
```
Shows which IPs are generating the most traffic.

**Panel 2 — Top Destination IPs**
```spl
index=* | top dest_ip
```
Shows most contacted destinations.

**Panel 3 — Activity Trend**
```spl
index=* | timechart count
```
Overall event volume over time.

**Panel 4 — Host Count**
```spl
index=* | stats dc(host) as host_count
```
Number of active hosts in the environment.

---

## Dashboard 3 — Process Overview

**Purpose:** Monitor process execution on endpoints

### Panels

**Panel 1 — Process Creation Events**
```spl
index=* EventCode=4688
| timechart count
```
Volume of process creation events over time.

**Panel 2 — PowerShell Activity**
```spl
index=* EventCode=4688 New_Process_Name="*powershell.exe*"
| timechart count
```
PowerShell execution monitoring — spikes are suspicious.

**Panel 3 — CMD Activity**
```spl
index=* EventCode=4688 New_Process_Name="*cmd.exe*"
| timechart count
```
Command prompt usage monitoring.

**Panel 4 — Process Trends**
```spl
index=* EventCode=4688
| top New_Process_Name
```
Most frequently created processes.
