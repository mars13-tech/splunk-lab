# Splunk Setup — Troubleshooting Notes

**Author:** Karthikeyan  
**Lab:** Splunk Enterprise Home Lab  
**Platform:** Windows Host  
**Last Updated:** May 2026  

---

## Why This File Exists

Every lab has problems. Most documentation skips the part where things break.

This file documents every real issue hit during Splunk setup and operation —
what went wrong, why it happened, and exactly how it was fixed.

These are the things no tutorial shows you.

---

## Issue 1 — Splunk Web Interface Not Loading

**Problem:**
Splunk installed successfully but browser showed connection refused
when navigating to `http://localhost:8000`

**Cause:**
Splunk service was not running after installation.
Installation does not automatically start the service.

**Fix:**
```
cd C:\Program Files\Splunk\bin
splunk start
splunk enable boot-start
```

**Verify it worked:**
```
splunk status
```
Should show: `splunkd is running`

**Lesson:**
Always verify the Splunk service is running before troubleshooting
anything else. 90% of "Splunk not working" issues start here.

---

## Issue 2 — No Events Appearing in Search

**Problem:**
Added data input but search returning zero results.
`index=main` showing empty.

**Cause:**
Data was ingesting into a different index than expected.
Default index assumption was wrong.

**Fix:**
Run a wildcard search to find where data actually landed:
```spl
index=* sourcetype=*
| head 20
```
Identified data was going into a custom index, not `main`.

Then narrowed down:
```spl
index=* sourcetype=syslog
| head 10
```

**Lesson:**
Never assume which index data lands in. Always verify with `index=*`
before building any detection logic on top of it.

---

## Issue 3 — Windows Event Logs Not Forwarding to Splunk

**Problem:**
Configured data input for Windows Event Logs but Security logs
not appearing in search results.

**Cause:**
`inputs.conf` was missing the correct WinEventLog stanza.
Splunk was not told to collect Security channel specifically.

**Fix:**
Located inputs.conf at:
```
C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf
```

Added the following stanza:
```
[WinEventLog://Security]
disabled = 0
index = main
sourcetype = WinEventLog:Security

[WinEventLog://System]
disabled = 0
index = main
sourcetype = WinEventLog:System

[WinEventLog://Application]
disabled = 0
index = main
sourcetype = WinEventLog:Application
```

Restarted the forwarder:
```
cd C:\Program Files\SplunkUniversalForwarder\bin
splunk restart
```

**Verify it worked:**
```spl
index=main sourcetype="WinEventLog:Security"
| head 10
```

**Lesson:**
inputs.conf controls what data gets collected. A missing stanza
means missing data. Always check inputs.conf first when logs are absent.

---

## Issue 4 — SPL Query Returning Wrong Results

**Problem:**
Detection query built but returning results that did not match
expected attack behaviour. False positives firing constantly.

**Cause:**
Field names assumed from documentation were wrong.
Actual field names in the data were different.

**Fix:**
Used raw event inspection to find correct field names:
```spl
index=main
| table _raw
| head 5
```

Then used `fieldsummary` to see all available fields:
```spl
index=main
| fieldsummary
| table field count
```

Rebuilt query using confirmed field names.

**Lesson:**
Never assume field names from documentation match your actual data.
Always inspect raw events first. One wrong field name = useless detection.

---

## Issue 5 — Correlation Search Not Triggering Alert

**Problem:**
Correlation rule written and saved but alert never fired
even when attack condition was met.

**Cause:**
Alert schedule was set to run every 24 hours.
Attack simulation completed before the scheduled run.

**Fix:**
Changed alert schedule to run every 5 minutes for lab testing:
- Saved search → Edit → Schedule
- Changed from `0 0 * * *` (daily) to `*/5 * * * *` (every 5 minutes)

Also changed the time range from `Last 24 hours` to `Last 15 minutes`
so the search only looked at recent events.

**Lesson:**
In a lab environment always set alert schedules to short intervals.
In production, balance between detection speed and system load.

---

## Issue 6 — Splunk Licence Warning Affecting Search

**Problem:**
Search results being truncated. Warning appearing:
`Your Splunk licence has exceeded its daily indexing limit`

**Cause:**
Splunk Free licence has a 500MB per day indexing limit.
Lab was ingesting too much data across multiple sources.

**Fix:**
Reduced data input volume by:
- Setting index=main monitor to only watch specific log files
- Removing duplicate data inputs
- Pausing ingestion when not actively using the lab

**Verify current usage:**
```spl
index=_internal source=*license_usage*
| stats sum(b) as bytes by pool
| eval MB=round(bytes/1024/1024,2)
| table pool MB
```

**Lesson:**
Free Splunk licence has limits. Monitor daily indexing volume
when running multiple data sources in a home lab.

---

## Issue 7 — Linux Auth Logs Not Parsing Correctly

**Problem:**
`/var/log/auth.log` ingesting but SPL field extractions
not working. `rex` command not finding source IP.

**Cause:**
Auth.log format on Ubuntu differed slightly from expected format.
The regex pattern needed adjusting for the actual log structure.

**Original regex (not working):**
```spl
| rex "from (?<src>\d+\.\d+\.\d+\.\d+) port"
```

**Fix:**
Inspected raw auth.log event in Splunk to see actual format:
```
May 24 03:21:44 mars-VirtualBox sshd[1234]: Failed password for root from 192.168.1.100 port 54321 ssh2
```

Adjusted regex to match actual format:
```spl
| rex "from (?<src>\d+\.\d+\.\d+\.\d+)"
```

**Verify it worked:**
```spl
index=main host="mars-VirtualBox" "Failed password"
| rex "from (?<src>\d+\.\d+\.\d+\.\d+)"
| table _time src
| head 10
```

**Lesson:**
Always inspect the raw event before writing regex.
Log formats vary by OS version and configuration.
One character difference in format breaks the entire extraction.

---

## Quick Reference — Common Splunk Commands for Troubleshooting

| Problem | Command |
|---|---|
| Find where data is going | `index=* \| stats count by index sourcetype` |
| Inspect raw events | `index=main \| table _raw \| head 10` |
| Check all field names | `index=main \| fieldsummary \| table field count` |
| Verify service running | `splunk status` |
| Check licence usage | `index=_internal source=*license_usage* \| stats sum(b) as bytes` |
| Check forwarder connectivity | `index=_internal source=*metrics.log* group=tcpin_connections` |
| Find ingestion errors | `index=_internal log_level=ERROR \| head 20` |

---

## Connected Files

- `setup/splunk-install-notes.md` — Full installation walkthrough
- `correlation-rules/brute-force-detection.md` — Rule built after fixing Issue 4
- `queries/basic-spl-cheatsheet.md` — SPL reference built from lab experience
- `notes/` — Day by day learning notes documenting lab progression
