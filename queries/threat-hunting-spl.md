# Threat Hunting SPL Cheat Sheet

## Overview

This document contains practical SPL queries used during threat hunting investigations.

Threat hunting is the proactive search for suspicious activity that may not have triggered an alert.

---

# Hunt 1: Failed Login Activity

## Objective

Identify accounts experiencing multiple failed login attempts.

```spl
index=* EventCode=4625
| stats count by Account_Name
| sort -count
```

Investigate:

* Targeted users
* Possible brute-force attacks

---

# Hunt 2: Failed Login Followed by Success

## Objective

Find possible password guessing attacks.

```spl
index=* (EventCode=4624 OR EventCode=4625)
| transaction Account_Name maxspan=15m
```

Investigate:

* Multiple failures followed by a successful login
* Potential account compromise

---

# Hunt 3: Top Source IP Addresses

## Objective

Identify noisy systems.

```spl
index=*
| top src_ip limit=20
```

Investigate:

* Excessive authentication attempts
* Network scanning behavior

---

# Hunt 4: Rare Processes

## Objective

Find unusual process executions.

```spl
index=* EventCode=4688
| rare New_Process_Name
```

Investigate:

* Unknown executables
* Malware execution

---

# Hunt 5: PowerShell Activity

## Objective

Identify PowerShell usage.

```spl
index=* powershell
```

Investigate:

* Encoded commands
* Suspicious scripts
* Administrative abuse

---

# Hunt 6: Command Prompt Activity

## Objective

Monitor command shell usage.

```spl
index=* cmd.exe
```

Investigate:

* Reconnaissance activity
* Batch script execution

---

# Hunt 7: Process Creation Trends

## Objective

Look for spikes in process activity.

```spl
index=* EventCode=4688
| timechart count
```

Investigate:

* Malware outbreaks
* Automated execution

---

# Hunt 8: Account Activity Across Multiple Hosts

## Objective

Detect possible lateral movement.

```spl
index=* EventCode=4624 Logon_Type=3
| stats dc(host) as systems by Account_Name
| where systems >= 3
```

Investigate:

* Credential theft
* Lateral movement

---

# Hunt 9: New User Accounts

## Objective

Monitor account creation events.

```spl
index=* EventCode=4720
```

Investigate:

* Unauthorized account creation
* Persistence mechanisms

---

# Hunt 10: Privileged Group Changes

## Objective

Monitor administrative privilege assignments.

```spl
index=* (EventCode=4728 OR EventCode=4732 OR EventCode=4756)
```

Investigate:

* Privilege escalation
* Insider threats

---

# Hunt 11: Excessive Authentication Failures

## Objective

Detect brute-force activity.

```spl
index=* EventCode=4625
| bucket _time span=5m
| stats count by src_ip _time
| where count >= 10
```

Investigate:

* Password spraying
* Brute-force attacks

---

# Hunt 12: Suspicious Login Times

## Objective

Identify logins outside normal business hours.

```spl
index=* EventCode=4624
| eval hour=strftime(_time,"%H")
| where hour<6 OR hour>22
| table _time Account_Name host
```

Investigate:

* Stolen credentials
* Unauthorized access

---

# Hunt 13: High Volume Activity From One Host

## Objective

Identify abnormal host behavior.

```spl
index=*
| stats count by host
| sort -count
```

Investigate:

* Compromised systems
* Logging anomalies

---

# Hunt 14: Most Active Users

## Objective

Find users generating excessive activity.

```spl
index=*
| stats count by Account_Name
| sort -count
```

Investigate:

* Compromised accounts
* Service account misuse

---

# Hunt 15: Threat Hunting Dashboard Query

## Objective

Generate activity trends.

```spl
index=*
| timechart count by sourcetype
```

Investigate:

* Sudden changes in log volume
* Missing data sources

---

# Important Windows Event IDs

| Event ID | Description                   |
| -------- | ----------------------------- |
| 4624     | Successful Login              |
| 4625     | Failed Login                  |
| 4688     | Process Creation              |
| 4720     | User Account Created          |
| 4728     | User Added to Security Group  |
| 4732     | User Added to Local Group     |
| 4756     | User Added to Universal Group |

---

# Threat Hunting Methodology

## Step 1

Define a hypothesis.

Example:
"An attacker may be attempting brute-force logins."

## Step 2

Search relevant logs.

## Step 3

Identify anomalies.

## Step 4

Validate findings.

## Step 5

Document evidence.

## Step 6

Escalate if necessary.

---

# Skills Demonstrated

* Threat Hunting
* Log Analysis
* SPL Development
* Detection Engineering
* Authentication Monitoring
* Process Monitoring
* Lateral Movement Detection
* Incident Investigation

---

Author: Karthi Keyan

Project: Splunk SOC Analyst Lab
