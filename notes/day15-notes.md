# DAY 15 — SPLUNK INSTALL + LOG INGESTION

**Author:** Karthikeyan  
**Platform:** Windows 11  
**Tool:** Splunk Enterprise 10.4.0  
**Objective:** Install Splunk and ingest logs for SOC analysis practice  

---

# Lab Goal

The goal of Day 15 was to:

* Install Splunk Enterprise Free
* Access Splunk Web
* Ingest logs into Splunk
* Understand core SIEM concepts
* Run first SPL searches

This marks the beginning of hands-on SIEM and SOC analyst training.

---

# What Is Splunk?

Splunk is a SIEM (Security Information and Event Management) platform used to:

* collect logs
* search logs
* investigate incidents
* detect threats
* build dashboards
* monitor systems

SOC analysts use Splunk daily for security monitoring and investigations.

---

# Installation Summary

| Component                   | Status  |
| --------------------------- | ------- |
| Splunk Enterprise Installed | Success |
| Splunk Web Accessible       | Success |
| Admin Login Working         | Success |
| Windows Logs Ingested       | Success |
| Search Functionality Tested | Success |

---

# Accessing Splunk Web

Splunk Web runs locally in the browser.

```text
http://localhost:8000
```

Default login:

* Username: `admin`
* Password: Created during installation

---

# Logs Ingested

## Windows Event Logs

Configured Splunk to ingest:

* Security logs
* System logs
* Application logs

These logs are critical for:

* login monitoring
* failed authentication detection
* privilege escalation analysis
* system auditing

---

## Sample Logs

Added sample data to practice:

* searching
* filtering
* aggregation
* statistics

---

## Apache Logs

Ingested web server logs to analyze:

* IP activity
* web requests
* suspicious traffic
* HTTP errors

---

# Core Splunk Concepts Learned

| Concept            | Explanation                         |
| ------------------ | ----------------------------------- |
| Index              | Storage location for events         |
| Sourcetype         | Format/type of logs                 |
| Host               | Device generating logs              |
| Event              | Single log entry                    |
| SPL                | Search Processing Language          |
| SplunkD            | Main Splunk background service      |
| Search & Reporting | Main application for investigations |

---

# First SPL Queries

## View All Events

```spl
index=*
```

Purpose:

* verify data ingestion
* confirm events are searchable

---

## Count Events by Sourcetype

```spl
index=* | stats count by sourcetype
```

Purpose:

* identify log types
* understand available data sources

---

## Detect Failed Logins

```spl
index=* EventCode=4625
```

Purpose:

* identify failed authentication attempts
* useful for brute force detection

---

# Important Windows Event IDs

| Event ID | Meaning                        |
| -------- | ------------------------------ |
| 4624     | Successful login               |
| 4625     | Failed login                   |
| 4688     | Process creation               |
| 4720     | User account created           |
| 4728     | User added to privileged group |

---

# Splunk Service Management

Navigate to:

```powershell
C:\Program Files\Splunk\bin
```

## Start Splunk

```powershell
splunk start
```

## Stop Splunk

```powershell
splunk stop
```

## Restart Splunk

```powershell
splunk restart
```

---

# Challenges Faced

| Issue                   | Resolution                               |
| ----------------------- | ---------------------------------------- |
| Splunk Web not loading  | Restarted Splunk service                 |
| Forgot service status   | Verified using Task Manager              |
| Log ingestion confusion | Used Search & Reporting to verify events |

---

# Skills Practiced

* SIEM Installation
* Windows Log Analysis
* SPL Searching
* Log Ingestion
* Security Monitoring
* Basic Threat Detection

---

# Key Takeaways

* Splunk installation on Windows is straightforward
* Logs become searchable immediately after ingestion
* SPL is powerful for investigations and monitoring
* Windows Event Logs contain valuable security telemetry
* Understanding logs is the foundation of SOC analysis

---


# Author

Karthikeyan

Blue Team | SOC Analyst in the Making

GitHub:
https://github.com/mars13-tech
