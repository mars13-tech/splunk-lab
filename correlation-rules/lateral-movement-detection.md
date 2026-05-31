# Lateral Movement Detection Correlation Rule

## Rule Name

Windows Lateral Movement Detection

---

## Description

This correlation rule detects potential lateral movement activity by monitoring Windows authentication events, privilege assignment events, and process creation events.

The rule is designed to identify attacker activity occurring after initial system compromise, where access is expanded to additional systems or elevated privileges are obtained.

---

## Detection Logic

1. Monitor Windows Security Event Logs.
2. Detect successful network logons.
3. Detect privileged account activity.
4. Detect process creation events.
5. Correlate authentication, privilege escalation, and execution activity.
6. Generate an alert when suspicious activity is observed.

---

## Data Sources

| Source               | Sourcetype           |
| -------------------- | -------------------- |
| WinEventLog:Security | WinEventLog:Security |

---

## Detection Query

```spl
index=main host="LAPTOP-7S5H8RIM"
(EventCode=4624 OR EventCode=4672 OR EventCode=4688)
| stats count values(EventCode) as event_codes by Account_Name Source_Network_Address
| where count > 0
| sort - count
```

---

## Correlation Conditions

| Condition            | Value           |
| -------------------- | --------------- |
| Successful Logon     | Event ID 4624   |
| Privilege Assignment | Event ID 4672   |
| Process Creation     | Event ID 4688   |
| Host                 | LAPTOP-7S5H8RIM |

---

## Alert Configuration

| Setting    | Value                      |
| ---------- | -------------------------- |
| Alert Name | Lateral_Movement_Detection |
| Severity   | Critical                   |
| Schedule   | Every 5 Minutes            |
| Trigger    | Number of Results > 0      |
| Action     | Email Notification         |

---

## Windows Event IDs

| Event ID | Description                              |
| -------- | ---------------------------------------- |
| 4624     | Successful Logon                         |
| 4672     | Special Privileges Assigned to New Logon |
| 4688     | Process Creation                         |

---

## Attack Indicators

* Successful network authentication
* Administrative privilege assignment
* Execution of commands or tools
* Access from unusual source systems
* Suspicious account activity

---

## MITRE ATT&CK Mapping

| Technique                         | ID    |
| --------------------------------- | ----- |
| Valid Accounts                    | T1078 |
| Remote Services                   | T1021 |
| Privilege Escalation              | T1068 |
| Command and Scripting Interpreter | T1059 |

---

## Investigation Steps

1. Identify the account involved.
2. Review the source network address.
3. Determine whether the login was expected.
4. Review Event ID 4672 activity.
5. Investigate processes created through Event ID 4688.
6. Identify any signs of privilege escalation.
7. Review related authentication events.
8. Determine whether additional systems were accessed.

---

## Attack Chain Position

```text
SSH Compromise
       ↓
Windows Authentication (4624)
       ↓
Privilege Assignment (4672)
       ↓
Process Execution (4688)
       ↓
Potential Lateral Movement
```

---

## SOC Analyst Response

### Priority

Critical

### Recommended Actions

* Validate user activity.
* Review source host information.
* Investigate executed commands.
* Examine privilege escalation events.
* Identify additional affected systems.
* Contain compromised accounts if necessary.
* Escalate to incident response procedures.

---

## Expected Outcome

This rule enables analysts to identify post-compromise activity and provides visibility into authentication, privilege escalation, and execution events that may indicate attacker movement within the environment.

---

## Detection Coverage

| Attack Phase         | Coverage |
| -------------------- | -------- |
| Initial Access       | No       |
| Credential Access    | Partial  |
| Valid Accounts       | Yes      |
| Lateral Movement     | Yes      |
| Privilege Escalation | Yes      |
| Execution            | Yes      |

---

## Business Impact

Successful lateral movement can allow attackers to:

* Access additional systems
* Escalate privileges
* Execute malicious tools
* Expand their presence within the environment
* Increase the impact of a security incident

Early detection reduces attacker dwell time and improves incident response effectiveness.

