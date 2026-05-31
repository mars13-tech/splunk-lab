# Splunk Dashboards

## Objective

Build professional Splunk dashboards to visualize security events and improve monitoring efficiency. The goal is to create dashboards that help SOC analysts quickly identify suspicious activity, authentication issues, network anomalies, and process execution trends.

---

# Dashboard 1 — Authentication Overview

## Purpose

Monitor login activity and detect authentication-related threats such as brute-force attacks, password spraying, and account compromise attempts.

### Panel 1 — Failed Logins Over Time

```spl
index=* EventCode=4625
| timechart count
```

**Visualization:** Line Chart

**Use Case:** Detect spikes in failed login attempts.

---

### Panel 2 — Successful Logins Over Time

```spl
index=* EventCode=4624
| timechart count
```

**Visualization:** Line Chart

**Use Case:** Monitor successful authentication activity.

---

### Panel 3 — Top Failed Accounts

```spl
index=* EventCode=4625
| stats count by Account_Name
| sort -count
| head 10
```

**Visualization:** Bar Chart

**Use Case:** Identify targeted user accounts.

---

### Panel 4 — Failed Logins by Host

```spl
index=* EventCode=4625
| stats count by host
| sort -count
```

**Visualization:** Column Chart

**Use Case:** Identify systems receiving excessive failed login attempts.

---

# Dashboard 2 — Network Overview

## Purpose

Monitor network communication patterns and identify unusual traffic behavior.

### Panel 1 — Top Source IPs

```spl
index=*
| top src_ip limit=10
```

**Visualization:** Bar Chart

**Use Case:** Identify the most active source IP addresses.

---

### Panel 2 — Top Destination IPs

```spl
index=*
| top dest_ip limit=10
```

**Visualization:** Bar Chart

**Use Case:** Identify the most contacted destinations.

---

### Panel 3 — Network Activity Trend

```spl
index=*
| timechart count
```

**Visualization:** Area Chart

**Use Case:** Detect unusual spikes in network activity.

---

### Panel 4 — Unique Hosts Reporting

```spl
index=*
| stats dc(host) as Unique_Hosts
```

**Visualization:** Single Value

**Use Case:** Monitor the number of systems actively sending logs.

---

# Dashboard 3 — Process Overview

## Purpose

Monitor process execution activity and identify suspicious command execution.

### Panel 1 — Most Executed Processes

```spl
index=* EventCode=4688
| stats count by New_Process_Name
| sort -count
| head 10
```

**Visualization:** Bar Chart

**Use Case:** Identify frequently executed processes.

---

### Panel 2 — PowerShell Activity

```spl
index=* powershell
| table _time host user Message
```

**Visualization:** Table

**Use Case:** Investigate PowerShell execution events.

---

### Panel 3 — CMD Activity

```spl
index=* cmd.exe
| table _time host user process
```

**Visualization:** Table

**Use Case:** Monitor command prompt activity.

---

### Panel 4 — Process Creation Trend

```spl
index=* EventCode=4688
| timechart count
```

**Visualization:** Line Chart

**Use Case:** Detect spikes in process creation events.

---

# Dashboard Design

## Title

SOC Security Monitoring Dashboard

## Description

Monitors authentication events, network activity, process execution, and security-related trends.

## Time Picker Options

* Last 24 Hours
* Last 7 Days
* Last 30 Days

---

# Skills Demonstrated

* Splunk Dashboard Studio
* SPL Query Development
* Log Analysis
* Security Monitoring
* Threat Hunting
* Data Visualization
* Security Operations Center (SOC) Monitoring

---

# Screenshots Collected

* Authentication Dashboard
* Network Dashboard
* Process Dashboard
* Dashboard Studio Configuration
* Panel Configuration Example

Stored in:

```text
screenshots/day18/
```

---

# Key Learning Outcomes

* Built three SOC-focused dashboards.
* Used SPL commands including `stats`, `top`, `timechart`, and `dc()`.
* Created visualizations for authentication, network, and process monitoring.
* Learned how dashboards help analysts detect threats faster.
* Improved understanding of Splunk Dashboard Studio.

---

# Interview Question

## Why build dashboards?

Dashboards transform large volumes of security logs into visual insights. They help analysts quickly identify abnormal behavior, monitor trends, detect attacks such as brute-force attempts, and reduce investigation time by presenting critical information in a clear and actionable format.

---

# Day 18 Completion Status

* [x] Authentication Dashboard Created
* [x] Network Dashboard Created
* [x] Process Dashboard Created
* [x] Minimum Four Panels Per Dashboard
* [x] Screenshots Saved
* [x] GitHub Documentation Completed
* [x] SPL Queries Explained
* [x] Dashboard Studio Practiced

**Day 18 Result:** Successfully created professional Splunk dashboards for SOC monitoring and threat detection.
