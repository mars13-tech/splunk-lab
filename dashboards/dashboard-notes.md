# Dashboard Notes — Splunk Lab

**Author:** Karthikeyan  
**Lab:** Splunk Enterprise Home Lab  
**Last Updated:** May 2026  

---

## Dashboards Overview

| File | What It Shows | SPL Used |
|---|---|---|
| splunk-home.png | Splunk Enterprise home page — search bar and dashboard overview | General view |
| spl-timechart.png | Event volume over time by sourcetype — timechart visualization | `index=main \| timechart span=1h count by sourcetype` |
| correlation-rule-results.png | Correlation rule results — failed logon count by source IP | `index=main EventCode=4625 \| stats count by src \| where count > 5` |

---

## Dashboard Descriptions

### splunk-home.png

**What it shows:**
Splunk Enterprise home page showing the main search bar, navigation menu, and available apps and dashboards.

**Why it matters:**
This is the starting point for every SOC analyst session in Splunk. The home page gives access to Search and Reporting, existing dashboards, and the data input configuration. Understanding the navigation is the foundation before writing any SPL.

**Key elements visible:**
- Search and Reporting app — primary analyst workspace
- Splunk navigation bar — apps, settings, activity
- Dashboard list — saved searches and visualizations
- Data input status — confirms data is flowing into the platform

**Connected file:**
`setup/splunk-install-notes.md` — how this environment was built

---

### spl-timechart.png

**What it shows:**
A timechart visualization showing event volume over time broken down by sourcetype. Each line on the chart represents a different log source ingesting into Splunk.

**SPL query used:**
```spl
index=main
| timechart span=1h count by sourcetype
```

**Why it matters:**
Timechart is one of the most important visualizations in SOC work. It shows:
- When events are occurring — time-based analysis
- Which log sources are most active
- Sudden spikes — potential attack activity
- Drops to zero — potential log source failure

A SOC analyst uses timecharts constantly during threat hunting and incident investigation to understand attack timelines.

**What to look for:**
- Sudden spike in any sourcetype — may indicate attack activity
- Flat line on expected sourcetype — log source may have stopped
- Unusual activity outside business hours — lateral movement indicator

**MITRE relevance:**
Timechart analysis supports detection of T1078 (Valid Accounts) and T1110 (Brute Force) by showing abnormal authentication event volume.

**Connected file:**
`queries/intermediate-spl-cheatsheet.md` — timechart and visualization commands

---

### correlation-rule-results.png

**What it shows:**
Results of a correlation rule detecting failed logon attempts — showing source IPs with failed login count above the threshold of 5, indicating potential brute force activity.

**SPL query used:**
```spl
index=main EventCode=4625
| rex "from (?<src>\d+\.\d+\.\d+\.\d+)"
| stats count by src
| where count > 5
```

**Why it matters:**
This is detection engineering in action. The query:
1. Filters for Event ID 4625 — Windows failed logon
2. Extracts source IP using regex
3. Groups by source IP and counts failures
4. Filters for IPs with more than 5 failures — brute force threshold

Any IP appearing in these results should be investigated immediately.

**MITRE ATT&CK:** T1110.001 — Brute Force: Password Guessing

**What to look for:**
- Single IP with very high count — targeted brute force
- Multiple IPs with similar counts — distributed attack or password spray
- Internal IP in results — may indicate compromised internal host

**Investigation next steps:**
1. Take the source IP from results
2. Search for successful logon from same IP
3. If successful logon found — escalate to P1 Critical
4. Block IP at firewall if confirmed malicious

**Connected files:**
- `correlation-rules/brute-force-detection.md` — full rule documentation
- `queries/threat-hunting-spl.md` — additional hunting queries
- Applied in `splunk-bruteforce-lateral-movement-detection` project

---

## How Dashboards Connect to Detection Engineering

```
splunk-home.png
        ↓
Environment confirmed — Splunk running
        ↓
spl-timechart.png
        ↓
Event volume baseline established — know what normal looks like
        ↓
correlation-rule-results.png
        ↓
Anomaly detected — failed logons above threshold
        ↓
correlation-rules/ — formal detection rule built from this finding
        ↓
Applied in splunk-bruteforce-lateral-movement-detection project
```

---

## Connected Files

- `screenshots/splunk-search-interface.png` — search interface view
- `screenshots/spl-query-results.png` — query results view
- `screenshots/threat-hunting-search.png` — threat hunting search
- `correlation-rules/brute-force-detection.md` — rule from correlation-rule-results
- `queries/basic-spl-cheatsheet.md` — SPL commands used
- `queries/threat-hunting-spl.md` — full hunting query library
- `notes/` — day by day learning notes
