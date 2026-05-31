# Brute Force Success Detection Correlation Rule

## Rule Name

SSH Brute Force Success Detection

---

## Description

This correlation rule detects a successful SSH authentication that occurs after multiple failed login attempts from the same source IP address within a defined time window.

The rule is designed to identify potential account compromise resulting from a successful brute force attack.

---

## Detection Logic

1. Monitor Linux authentication logs.
2. Identify failed SSH login events.
3. Identify successful SSH login events.
4. Group events by source IP address.
5. Correlate failed and successful authentication events within a 10-minute period.
6. Trigger an alert when a successful login follows multiple failed attempts.

---

## Data Sources

| Source            | Sourcetype |
| ----------------- | ---------- |
| /var/log/auth.log | auth       |

---

## Detection Query

```spl
index=main host="mars-VirtualBox"
("Failed password" OR "Accepted password")
| rex "from (?<src>\d+\.\d+\.\d+\.\d+)"
| transaction src maxspan=10m
| search eventcount > 5 AND "Accepted password"
```

---

## Correlation Conditions

| Condition        | Value             |
| ---------------- | ----------------- |
| Failed Logins    | More than 5       |
| Successful Login | Required          |
| Time Window      | 10 Minutes        |
| Grouping Field   | Source IP Address |

---

## Alert Configuration

| Setting    | Value                   |
| ---------- | ----------------------- |
| Alert Name | SSH_Brute_Force_Success |
| Severity   | Critical                |
| Schedule   | Every 5 Minutes         |
| Trigger    | Number of Results > 0   |
| Action     | Email Notification      |

---

## Attack Indicators

* Repeated failed SSH logins
* Password guessing activity
* Successful authentication after failures
* Potential credential compromise

---

## MITRE ATT&CK Mapping

| Technique      | ID    |
| -------------- | ----- |
| Brute Force    | T1110 |
| Valid Accounts | T1078 |

---

## Investigation Steps

1. Identify the affected account.
2. Determine the source IP address.
3. Review authentication history.
4. Verify whether the login was authorized.
5. Investigate post-authentication activity.
6. Review lateral movement indicators.
7. Reset credentials if compromise is confirmed.
8. Escalate incident response procedures if necessary.

---

## Expected Outcome

This rule helps analysts identify successful account compromise attempts that originate from brute force activity and enables rapid investigation before further attacker actions occur.

---

## Attack Chain Position

```text
Failed SSH Login Attempts
          ↓
Successful SSH Authentication
          ↓
Potential Account Compromise
          ↓
Lateral Movement
          ↓
Privilege Escalation
```

---

## SOC Analyst Response

### Priority

Critical

### Recommended Actions

* Validate user activity.
* Review source IP reputation.
* Investigate subsequent login activity.
* Monitor for lateral movement.
* Contain compromised accounts if required.
