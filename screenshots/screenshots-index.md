# Screenshots Index — Splunk Lab

**Author:** Karthikeyan  
**Lab:** Splunk Enterprise Home Lab  
**Last Updated:** May 2026  

---

## Screenshots Overview

| File | What It Shows | SPL Used |
|---|---|---|
| splunk-search-interface.png | Splunk search interface — timeline, results, field sidebar | General search view |
| spl-query-results.png | SPL query results showing sourcetype distribution | `index=main \| stats count by sourcetype` |
| threat-hunting-search.png | Threat hunting search — failed logon analysis by account | `index=main EventCode=4625 \| stats count by Account_Name` |

---

## Screenshot Descriptions

### splunk-search-interface.png

**What it shows:**
The Splunk Enterprise search interface showing the full analyst workspace — search bar, time range selector, results panel, event timeline, and field sidebar.

**Why it matters:**
This is the primary interface a SOC analyst works in during threat hunting and investigation. The field sidebar on the left shows available fields extracted from ingested logs — understanding which fields exist is the foundation of writing effective SPL queries.

**Key elements visible:**
- Search bar — where SPL queries are entered
- Time range picker — controls the investigation window
- Event timeline — shows event distribution over time
- Field sidebar — extracted fields from ingested log sources
- Results panel — raw events or statistical results

---

### spl-query-results.png

**What it shows:**
Results of an SPL query showing event count distributed by sourcetype — giving a clear view of what data sources are actively ingesting into the lab.

**SPL query used:**
```spl
index=main
| stats count by sourcetype
| sort -count
```

**Why it matters:**
Before writing any detection logic, a SOC analyst must know what data is available. This query answers: what log sources are ingesting, how much data from each source, and which sourcetype to target for specific detections.

**What to look for:**
- Which sourcetype has the highest event count
- Whether expected log sources (WinEventLog:Security, syslog) are present
- Any unexpected sourcetypes that may indicate misconfiguration

**Connected file:**
`queries/basic-spl-cheatsheet.md` — contains the foundational SPL commands used here

---

### threat-hunting-search.png

**What it shows:**
Threat hunting search targeting failed Windows logon events — grouped by account name to identify accounts being targeted by brute force or credential stuffing.

**SPL query used:**
```spl
index=main EventCode=4625
| stats count by Account_Name
| sort -count
| head 10
```

**Why it matters:**
Event ID 4625 is one of the most important Windows Security events for a SOC analyst. It fires every time a logon attempt fails. Grouping by Account_Name reveals which accounts are being targeted — a high count on a single account indicates brute force activity.

**MITRE ATT&CK:** T1110 — Brute Force

**What to look for:**
- Any account with unusually high failed logon count
- Admin or service accounts being targeted
- Multiple accounts with similar counts — may indicate password spraying

**Investigation next steps:**
1. Pivot on the highest count account
2. Check source IP of the failed attempts
3. Correlate with successful logons from same source
4. Escalate if successful logon found after multiple failures

**Connected files:**
- `correlation-rules/brute-force-detection.md` — detection rule built on this query
- `queries/threat-hunting-spl.md` — full threat hunting query library

---

## How These Screenshots Connect to the Lab

```
splunk-search-interface.png
        ↓
Understanding the workspace
        ↓
spl-query-results.png
        ↓
Knowing what data is available
        ↓
threat-hunting-search.png
        ↓
Hunting for specific threats
        ↓
correlation-rules/ — detection rules built from hunting findings
        ↓
Applied in splunk-bruteforce-lateral-movement-detection project
```

---

## Connected Files

- `queries/basic-spl-cheatsheet.md` — SPL commands used in screenshots
- `queries/threat-hunting-spl.md` — full threat hunting query library
- `correlation-rules/brute-force-detection.md` — rule built from threat-hunting-search
- `dashboards/dashboard-notes.md` — dashboard screenshots and explanations
- `notes/` — day by day learning notes documenting lab progression
