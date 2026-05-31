# Splunk Lab — Splunk Enterprise Home Lab

**Author:** Karthikeyan  
**Target Role:** SOC Analyst (Blue Team)  
**Platform:** Splunk Enterprise (Free Licence)  
**Host:** Windows  
**Status:** Active  
🔗 [LinkedIn](https://www.linkedin.com/in/karthi-keyan-9042862bb) · 🐙 [GitHub](https://github.com/mars13-tech)

---

## What This Is

A home lab built on Splunk Enterprise to develop real SOC analyst skills —
SPL query writing, threat hunting, correlation rule building, and
detection engineering using Windows and Linux log sources.

This lab was the foundation for the applied detection project:
🔗 [splunk-soc-detection](https://github.com/mars13-tech/splunk-soc-detection)

---

## Lab Architecture

```
Windows Event Logs (Security, System, Application)
        ↓
Splunk Universal Forwarder
        ↓
Splunk Enterprise (Windows Host)
        ↓
Search and Reporting (SPL Queries)
        ↓
Correlation Rules → Alerts → Detection
```

---

## Repository Structure

```
splunk-lab/
│
├── README.md
│
├── setup/
│   ├── splunk-install-notes.md      — Splunk Enterprise installation walkthrough
│   └── troubleshooting-notes.md     — Real issues hit during setup and how they were fixed
│
├── notes/
│   ├── splunk-setup-and-data-ingestion.md  — Data sources, indexes, sourcetypes
│   ├── spl-fundamentals.md                 — Core SPL commands and syntax
│   ├── threat-hunting-with-spl.md          — Threat hunting queries and methodology
│   ├── correlation-rules-and-alerts.md     — Building correlation searches and alerts
│   └── detection-engineering-and-tuning.md — Detection logic and false positive reduction
│
├── queries/
│   ├── basic-spl-cheatsheet.md      — Foundational SPL commands reference
│   ├── intermediate-spl-cheatsheet.md — Intermediate SPL commands reference
│   └── threat-hunting-spl.md        — SPL queries for threat hunting
│
├── correlation-rules/
│   ├── brute-force-detection.md     — T1110 — Brute force login detection
│   ├── brute-force-success.md       — T1078 — Successful login after brute force
│   └── lateral-movement-detection.md — T1021 — Lateral movement via remote services
│
├── dashboards/
│   ├── dashboard-notes.md           — Dashboard descriptions and query explanations
│   ├── correlation-rule-results.png — Correlation rule firing on failed logons
│   ├── spl-timechart.png            — Event volume timechart by sourcetype
│   └── splunk-home.png              — Splunk Enterprise home interface
│
└── screenshots/
    ├── screenshots-index.md         — Index and description of all screenshots
    ├── splunk-search-interface.png  — Splunk search workspace
    ├── spl-query-results.png        — SPL query results showing sourcetype distribution
    └── threat-hunting-search.png    — Threat hunting search for failed logons
```

---

## Correlation Rules

| Rule | MITRE Technique | ID | Severity |
|---|---|---|---|
| Brute Force Detection | Brute Force: Password Guessing | T1110.001 | High |
| Brute Force Success Detection | Valid Accounts | T1078 | Critical |
| Lateral Movement Detection | Remote Services: SSH | T1021.004 | Critical |

---

## Key SPL Queries

### Failed Logon Detection

```spl
index=main EventCode=4625
| stats count by Account_Name
| sort -count
```

### Brute Force Source IP Detection

```spl
index=main EventCode=4625
| rex "from (?<src>\d+\.\d+\.\d+\.\d+)"
| stats count by src
| where count > 10
```

### Event Volume Timechart

```spl
index=main
| timechart span=1h count by sourcetype
```

### Privilege Escalation Detection

```spl
index=main EventCode=4672
| stats count by Account_Name
| sort -count
```

### Suspicious Process Execution

```spl
index=main EventCode=4688
| search Image="*cmd*" OR Image="*powershell*"
| table _time Account_Name Image CommandLine
```

> Full query library → [queries/threat-hunting-spl.md](./queries/threat-hunting-spl.md)

---

## MITRE ATT&CK Coverage

| Technique | ID | Detection |
|---|---|---|
| Brute Force: Password Guessing | T1110.001 | Failed logon count threshold |
| Valid Accounts | T1078 | Successful login after failures |
| Remote Services: SSH | T1021.004 | Network logon from unexpected source |
| Privilege Escalation | T1068 | Event ID 4672 monitoring |
| Command and Scripting Interpreter | T1059.004 | Event ID 4688 process monitoring |

---

## Skills Demonstrated

| Skill | Evidence |
|---|---|
| Splunk Administration | setup/ — full install and configuration documented |
| SPL Query Writing | queries/ — 3 cheatsheet files covering basic to threat hunting |
| Correlation Rule Building | correlation-rules/ — 3 rules with MITRE mapping |
| Threat Hunting | queries/threat-hunting-spl.md · screenshots/threat-hunting-search.png |
| Dashboard Development | dashboards/ — 3 dashboard screenshots with full explanations |
| Troubleshooting | setup/troubleshooting-notes.md — 7 real issues documented |
| Detection Engineering | Applied in splunk-soc-detection project |

---

## What I Learned That Wasn't in Any Tutorial

The hardest part was not writing the SPL query.

It was knowing which field to query.

Auth.log on Ubuntu uses completely different field names than
WinEventLog:Security on Windows. The same attack — a failed login —
looks completely different in each log source. Understanding that
difference is what separates someone who can write SPL from someone
who can actually hunt threats across a real environment.

---

## Applied Project

Skills built in this lab were applied to a full detection engineering project:
🔗 [splunk-soc-detection](https://github.com/mars13-tech/splunk-soc-detection)

That project simulates a real brute force and lateral movement attack
across Linux and Windows — detected entirely using Splunk.

---

## Author

**Karthikeyan**  
Cybersecurity Engineering Student | Blue Team | SOC Analyst in the Making  
🔗 [LinkedIn](https://www.linkedin.com/in/karthi-keyan-9042862bb)  
🐙 [GitHub](https://github.com/mars13-tech)
