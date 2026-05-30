# Brute Force Login Detection

## Overview

This detection identifies potential brute force login attacks by monitoring excessive failed login attempts within a short time period.

Brute force attacks occur when an attacker repeatedly attempts different passwords against a user account until successful authentication is achieved.

Windows records failed login attempts using Event ID 4625.

---

# Detection Information

## Detection Name

```text
Brute Force Login Detection
```

## MITRE ATT&CK

```text
T1110 - Brute Force
```

## Severity

```text
Medium
```

## Data Source

```text
Windows Security Logs
```

## Event ID

```text
4625
```

---

# Detection Logic

Trigger an alert when:

```text
5 or more failed logins
within 5 minutes
on the same host
```

In a production environment, the threshold would typically be:

```text
10 or more failed logins
within 5 minutes
from the same source IP
```

For this lab environment, the detection is based on host activity because only one Windows system is available.

---

# SPL Query

```spl
index=* EventCode=4625
| bucket _time span=5m
| stats count by host _time
| where count >= 5
| sort -count
```

---

# Query Breakdown

## Search Failed Logins

```spl
index=* EventCode=4625
```

Searches for Windows failed authentication events.

---

## Create Time Buckets

```spl
bucket _time span=5m
```

Groups events into 5-minute intervals.

Example:

```text
07:00 - 07:05
07:05 - 07:10
07:10 - 07:15
```

---

## Count Failed Logins

```spl
stats count by host _time
```

Counts failed login attempts for each host within each time bucket.

---

## Apply Detection Threshold

```spl
where count >= 5
```

Returns only suspicious activity meeting the brute force threshold.

---

# Alert Configuration

## Alert Title

```text
Brute Force Login Detection
```

## Schedule

```text
Run Every 5 Minutes
```

## Trigger Condition

```text
Number of Results > 0
```

## Severity

```text
Medium
```

---

# Testing Procedure

## Generate Failed Logins

1. Lock the Windows workstation.
2. Enter an incorrect password multiple times.
3. Generate Event ID 4625 logs.
4. Verify events appear in Splunk.

---

## Verification Query

```spl
index=* EventCode=4625
| stats count by host
```

Expected Result:

```text
Host shows increasing failed login count.
```

---

## Detection Validation

Run:

```spl
index=* EventCode=4625
| bucket _time span=5m
| stats count by host _time
```

Confirm that the count exceeds the configured threshold.

---

# Investigation Steps

When the alert fires:

1. Identify affected host.
2. Review failed login volume.
3. Check targeted user accounts.
4. Search for successful logins after failures.
5. Determine whether activity is legitimate or malicious.

Useful follow-up search:

```spl
index=* (EventCode=4624 OR EventCode=4625)
```

Look for patterns such as:

```text
4625
4625
4625
4624
```

which may indicate a successful compromise after repeated failures.

---

# Expected Outcome

Successful detection of excessive failed authentication attempts that may indicate:

* Password guessing
* Password spraying
* Automated brute force attacks
* Account compromise attempts
