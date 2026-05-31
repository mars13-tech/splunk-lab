# Brute Force Detection Correlation Rule

## Rule Name

SSH Brute Force Detection

---

## Description

This correlation rule detects potential SSH brute force attacks against the Ubuntu server by identifying a high number of failed authentication attempts originating from the same source IP address.

The rule is designed to identify attackers attempting to gain unauthorized access through password guessing.

---

## Detection Logic

1. Monitor Linux authentication logs.
2. Identify SSH failed login events.
3. Extract source IP addresses.
4. Count failed login attempts per source IP.
5. Trigger an alert when the number of failed attempts exceeds the defined threshold.

---

## Data Sources

| Source            | Sourcetype |
| ----------------- | ---------- |
| /var/log/auth.log | auth       |

---

## Detection Query

```spl
index=main host="mars-VirtualBox" "Failed password"
| rex "from (?<src>\d+\.\d+\.\d+\.\d+)"
| stats count as failed_attempts by src
| where failed_attempts > 10
| sort - failed_attempts
```

---

## Correlation Conditions

| Condition      | Value                 |
| -------------- | --------------------- |
| Event Type     | Failed SSH Login      |
| Threshold      | More than 10 attempts |
| Time Window    | 5 Minutes             |
| Grouping Field | Source IP Address     |

---

## Alert Configuration

| Setting    | Value                     |
| ---------- | ------------------------- |
| Alert Name | SSH_Brute_Force_Detection |
| Severity   | High                      |
| Schedule   | Every 5 Minutes           |
| Trigger    | Number of Results > 0     |
| Action     | Email Notification        |

---

## Attack Indicators

* Multiple failed SSH login attempts
* Repeated authentication failures
* Password guessing behavior
* Single source IP generating excessive failures

---

## MITRE ATT&CK Mapping

| Technique   | ID    |
| ----------- | ----- |
| Brute Force | T1110 |

---

## Investigation Steps

1. Identify the attacking IP address.
2. Determine whether the source is internal or external.
3. Review authentication activity from the source.
4. Check for successful login events following failures.
5. Investigate related activity on the target host.
6. Consider blocking the source IP if malicious.

---

## Expected Outcome

This rule provides early detection of SSH brute force activity and helps analysts identify unauthorized access attempts before a successful compromise occurs.

