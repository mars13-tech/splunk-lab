# Lateral Movement Detection

## Overview

This detection identifies potential lateral movement activity by monitoring successful network logons across systems.

Lateral movement occurs when an attacker uses compromised credentials to move from one machine to another after gaining initial access.

Windows records successful authentication events using Event ID 4624.

---

# Detection Information

## Detection Name

```text
Possible Lateral Movement
```

## MITRE ATT&CK

```text
T1021 - Remote Services
T1078 - Valid Accounts
```

## Severity

```text
High
```

## Data Source

```text
Windows Security Logs
```

## Event ID

```text
4624
```

## Logon Type

```text
3
```

(Network Logon)

---

# Attack Scenario

An attacker compromises a user account and begins accessing multiple systems.

Example:

```text
User-PC
   ↓
File Server
   ↓
Application Server
   ↓
Domain Controller
```

This behavior is often observed during:

* Internal reconnaissance
* Credential abuse
* Lateral movement
* Privilege escalation campaigns

---

# Detection Logic

Identify successful network logons that may indicate movement between systems.

Production environments typically detect:

```text
Same account
+
3 or more hosts
+
Within 1 hour
```

Example production query:

```spl
index=* EventCode=4624 Logon_Type=3
| bucket _time span=1h
| stats dc(host) as systems values(host) as hosts by Account_Name _time
| where systems >= 3
```

---

# Lab Detection Query

Because this lab contains a single Windows host, true multi-host lateral movement cannot be simulated.

The lab detection focuses on identifying network authentication activity.

```spl
index=* EventCode=4624
| rex field=_raw "Logon Type:\s+(?<Logon_Type>\d+)"
| search Logon_Type=3
| stats count by host
```

---

# Query Breakdown

## Search Successful Logons

```spl
index=* EventCode=4624
```

Returns successful authentication events.

---

## Extract Logon Type

```spl
rex field=_raw "Logon Type:\s+(?<Logon_Type>\d+)"
```

Parses the Logon Type value from the raw Windows event.

---

## Filter Network Logons

```spl
search Logon_Type=3
```

Displays only network authentication events.

---

## Count Activity

```spl
stats count by host
```

Shows the volume of network logons observed on each host.

---

# Alert Configuration

## Alert Title

```text
Possible Lateral Movement
```

## Schedule

```text
Run Every Hour
```

## Trigger Condition

```text
Number of Results > 0
```

## Severity

```text
High
```

---

# Testing Procedure

## Verify Successful Logons

Run:

```spl
index=* EventCode=4624
| stats count
```

Confirm successful login events are being collected.

---

## Verify Logon Types

Run:

```spl
index=* EventCode=4624
| rex field=_raw "Logon Type:\s+(?<Logon_Type>\d+)"
| stats count by Logon_Type
```

Expected output:

```text
Logon_Type    Count
2             XX
3             XX
10            XX
```

---

## Verify Detection Query

Run:

```spl
index=* EventCode=4624
| rex field=_raw "Logon Type:\s+(?<Logon_Type>\d+)"
| search Logon_Type=3
```

Confirm network authentication events are returned.

---

# Investigation Steps

When the alert fires:

### Identify User Account

```spl
index=* EventCode=4624
| stats count by Account_Name
```

---

### Review Host Activity

```spl
index=* EventCode=4624
| stats values(host) by Account_Name
```

---

### Check Privileged Logons

Search for:

```text
4672
```

Special privileges assigned to a new logon.

---

### Review Process Execution

Search for:

```text
4688
```

Process creation events following authentication.

---

# Expected Outcome

Detection identifies authentication activity that may be associated with:

* Credential theft
* Remote access
* Internal reconnaissance
* Lateral movement
* Account compromise

---

# Lab Environment

Host:

```text
LAPTOP-7S5H8RIM
```

Log Source:

```text
WinEventLog:Security
```

SIEM:

```text
Splunk Enterprise
```

---

# Screenshots

Store screenshots in:

```text
screenshots/
```

Required:

```text
lateral-movement-query.png
lateral-movement-alert.png
triggered-alerts.png
```

---

# Key Takeaway

Lateral movement is one of the most important attack phases to detect because it often indicates that an attacker already has valid credentials inside the environment. Monitoring successful network logons and authentication patterns helps SOC analysts identify compromised accounts before critical systems are reached.
