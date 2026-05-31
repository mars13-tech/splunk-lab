# Splunk Correlation Searches

## Objective

Learn how to create and test Splunk correlation searches for detecting:

1. Brute Force Login Attempts
2. Potential Lateral Movement Activity

This lab focuses on transforming raw log events into actionable security detections.

---

# What is a Correlation Search?

A correlation search analyzes multiple events over a defined period of time to identify suspicious behavior patterns that would not be obvious from a single event.

Examples:

* Brute Force Attacks
* Lateral Movement
* Privilege Escalation
* Account Compromise

SOC teams use correlation searches to automate threat detection and generate alerts.

---

# Detection 1 – Brute Force Login Detection

## Attack Scenario

An attacker repeatedly attempts different passwords against an account.

Windows generates:

```text
Event ID 4625
```

for every failed logon attempt.

---

## SPL Query

```spl
index=* EventCode=4625
| bucket _time span=5m
| stats count by host _time
| where count >= 5
| sort -count
```

---

## Query Explanation

### EventCode=4625

Searches for failed logon events.

### bucket _time span=5m

Groups events into 5-minute windows.

### stats count by host _time

Counts failed logins per host within each time bucket.

### where count >= 5

Triggers when 5 or more failures occur within 5 minutes.

---

## Alert Configuration

Title:

```text
Brute Force Login Detection
```

Schedule:

```text
Every 5 Minutes
```

Trigger Condition:

```text
Number of Results > 0
```

Severity:

```text
Medium
```

---

# Detection 2 – Potential Lateral Movement

## Attack Scenario

An attacker steals credentials and authenticates to multiple systems.

Common Windows Event:

```text
Event ID 4624
```

Common Logon Type:

```text
Logon Type 3
```

(Network Logon)

---

## SPL Query

```spl
index=* EventCode=4624
| rex field=_raw "Logon Type:\s+(?<Logon_Type>\d+)"
| search Logon_Type=3
| stats count by host
```

---

## Query Explanation

### EventCode=4624

Searches for successful logons.

### rex

Extracts Logon Type from raw Windows event logs.

### search Logon_Type=3

Filters for network authentication events.

### stats count by host

Displays hosts receiving network logons.

---

## Alert Configuration

Title:

```text
Possible Lateral Movement
```

Schedule:

```text
Every Hour
```

Trigger Condition:

```text
Number of Results > 0
```

Severity:

```text
High
```

---

# Lab Environment

Operating System:

```text
Windows 10
```

SIEM:

```text
Splunk Enterprise
```

Log Source:

```text
WinEventLog:Security
```

Host:

```text
LAPTOP-7S5H8RIM
```

---

# Testing Procedure

## Brute Force Detection

1. Lock Windows workstation.
2. Enter incorrect password multiple times.
3. Generate Event ID 4625 logs.
4. Run SPL query.
5. Verify alert triggers.

---

## Lateral Movement Detection

1. Search Event ID 4624 logs.
2. Extract Logon Type.
3. Verify Logon Type 3 activity.
4. Execute correlation search.
5. Confirm alert configuration.
---

