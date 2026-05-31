# Basic SPL Cheat Sheet

## Overview

This document contains basic Splunk Processing Language (SPL) queries used during SOC analyst investigations.

---

## 1. Show All Logs

```spl
index=*
```

Purpose:

* Displays all available events.
* Useful for verifying data ingestion.

---

## 2. Search Failed Logins

```spl
index=* EventCode=4625
```

Purpose:

* Shows failed authentication attempts.
* Useful for brute-force detection.

---

## 3. Search Successful Logins

```spl
index=* EventCode=4624
```

Purpose:

* Shows successful user logins.
* Useful for user activity monitoring.

---

## 4. Count Total Events

```spl
index=*
| stats count
```

Purpose:

* Returns total number of events.

---

## 5. Count Failed Logins by User

```spl
index=* EventCode=4625
| stats count by Account_Name
```

Purpose:

* Identifies users receiving the most failed login attempts.

---

## 6. Display Important Fields

```spl
index=* EventCode=4625
| table _time Account_Name host src_ip
```

Purpose:

* Creates a clean investigation table.

---

## 7. Top Source IP Addresses

```spl
index=*
| top src_ip
```

Purpose:

* Finds most active source IP addresses.

---

## 8. Rare Processes

```spl
index=*
| rare process_name
```

Purpose:

* Identifies uncommon processes for threat hunting.

---

## 9. Login Activity Timeline

```spl
index=* EventCode=4625
| timechart count
```

Purpose:

* Visualizes failed login activity over time.

---

## 10. Top Event Codes

```spl
index=*
| top EventCode
```

Purpose:

* Shows most common Windows event IDs.

---

# Important Windows Event IDs

| Event ID | Description      |
| -------- | ---------------- |
| 4624     | Successful Login |
| 4625     | Failed Login     |
| 4688     | Process Creation |

---

# Core SPL Commands

## search

Searches events.

```spl
index=* EventCode=4625
```

## stats

Performs calculations and aggregation.

```spl
index=*
| stats count by host
```

## table

Displays selected fields.

```spl
index=*
| table host src_ip user
```

## top

Displays most common values.

```spl
index=*
| top src_ip
```

## rare

Displays least common values.

```spl
index=*
| rare process_name
```

## timechart

Creates time-based visualizations.

```spl
index=* EventCode=4625
| timechart count
```

---

# SOC Analyst Use Cases

* Authentication Monitoring
* Brute Force Detection
* User Activity Analysis
* Host Monitoring
* Threat Hunting
* Security Investigations

---

Author: Karthi Keyan

Project: Splunk SOC Analyst Lab
