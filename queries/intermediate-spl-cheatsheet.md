# Intermediate SPL Cheat Sheet

## Overview

This document contains intermediate Splunk Processing Language (SPL) commands commonly used by SOC analysts during investigations, threat hunting, and detection engineering.

---

# 1. eval

## Purpose

Creates new fields or performs calculations.

### Example 1

```spl
index=* EventCode=4625
| eval severity="High"
| table _time Account_Name severity
```

Use Case:

* Assign alert severity levels.

---

### Example 2

```spl
index=*
| eval full_name=first_name." ".last_name
```

Use Case:

* Combine multiple fields.

---

# 2. dedup

## Purpose

Removes duplicate values.

### Example 1

```spl
index=*
| dedup src_ip
| table src_ip
```

Use Case:

* Show unique source IP addresses.

---

### Example 2

```spl
index=*
| dedup Account_Name
| table Account_Name
```

Use Case:

* Display unique users.

---

# 3. rex

## Purpose

Extract fields from raw log data using regular expressions.

### Extract Username

```spl
index=*
| rex field=_raw "Account Name:\s+(?<username>\w+)"
| table username
```

Use Case:

* Extract usernames from Windows logs.

---

### Extract IPv4 Address

```spl
index=*
| rex field=_raw "(?<ip>\d+\.\d+\.\d+\.\d+)"
| table ip
```

Use Case:

* Extract IP addresses from log messages.

---

### Extract Email Address

```spl
index=*
| rex field=_raw "(?<email>[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,})"
| table email
```

Use Case:

* Identify email addresses within logs.

---

# 4. transaction

## Purpose

Groups related events together.

### Example 1

```spl
index=* EventCode=4624
| transaction Account_Name maxspan=10m
```

Use Case:

* Track user login sessions.

---

### Example 2

```spl
index=* (EventCode=4624 OR EventCode=4625)
| transaction Account_Name maxspan=15m
```

Use Case:

* Investigate failed logins followed by successful logins.

---

# 5. lookup

## Purpose

Enrich logs using external data sources.

### Example

```spl
index=*
| lookup malicious_ips.csv ip AS src_ip OUTPUT threat_level
```

Use Case:

* Match source IPs against threat intelligence.

---

# 6. stats

## Purpose

Aggregate and summarize data.

### Count Events By User

```spl
index=*
| stats count by Account_Name
```

---

### Count Unique Hosts

```spl
index=*
| stats dc(host)
```

---

### Top Users

```spl
index=*
| stats count by Account_Name
| sort -count
```

---

# 7. sort

## Purpose

Sort search results.

### Descending Order

```spl
index=*
| stats count by src_ip
| sort -count
```

---

### Ascending Order

```spl
index=*
| stats count by src_ip
| sort count
```

---

# 8. where

## Purpose

Filter results based on conditions.

### Example

```spl
index=*
| stats count by src_ip
| where count > 10
```

Use Case:

* Detect suspicious activity thresholds.

---

# Investigation Examples

## Brute Force Detection

```spl
index=* EventCode=4625
| bucket _time span=5m
| stats count by src_ip _time
| where count >= 10
```

Logic:

* Detect 10+ failed logins from the same IP within 5 minutes.

---

## Lateral Movement Detection

```spl
index=* EventCode=4624 Logon_Type=3
| stats dc(host) as systems by Account_Name
| where systems >= 3
```

Logic:

* Detect one account authenticating to multiple systems.

---

# Important Commands Summary

| Command     | Purpose                   |
| ----------- | ------------------------- |
| eval        | Create new fields         |
| dedup       | Remove duplicates         |
| rex         | Extract fields with regex |
| transaction | Group related events      |
| lookup      | Enrich logs               |
| stats       | Aggregate data            |
| sort        | Sort results              |
| where       | Apply conditions          |

---

# SOC Analyst Use Cases

* Threat Hunting
* Log Enrichment
* Session Tracking
* Detection Engineering
* IOC Matching
* Brute Force Detection
* Lateral Movement Detection
* Security Investigations

---

Author: Karthi Keyan

Project: Splunk SOC Analyst Lab
