# Splunk Installation Notes — Windows

**Author:** Karthikeyan  
**Date:** May 2026  
**Platform:** Windows 11  
**Splunk Version:** Splunk Enterprise 10.4.0 (Free Trial)  
**Install Time:** 10 minutes  
**Status:** Successfully installed and running  

---

## What Is Splunk Enterprise Free?

Splunk Enterprise Free allows ingesting up to 500MB of data per day.
No license required for home lab use. Full SPL functionality available.
Perfect for SOC analyst skill building and detection engineering practice.

---

## Download

1. Go to splunk.com/en_us/download/splunk-enterprise.html
2. Create a free Splunk account if you do not have one
3. Select Windows as the platform
4. Download the .msi installer — Splunk Enterprise 10.4.0
5. File size approximately 450MB

---

## Installation Steps

### Step 1 — Run the Installer

1. Double click the downloaded .msi file
2. Click Yes on the UAC prompt
3. Splunk Setup Wizard opens

---

### Step 2 — Accept License Agreement

1. Check the box to accept the License Agreement
2. Select Splunk Enterprise Free License
3. Click Next

---

### Step 3 — Set Admin Credentials

1. Create your admin username — recommended: admin
2. Set a strong password — you will use this every time you log in
3. Click Next

Note: Do not forget this password. There is no recovery option without reinstalling.

---

### Step 4 — Install Location

Default install path:

C:\Program Files\Splunk

Keep the default unless you have a specific reason to change it.
Click Install

---

### Step 5 — Installation Completes

1. Installation takes approximately 10 minutes
2. Check Launch browser with Splunk Enterprise when prompted
3. Click Finish

---

### Step 6 — Access Splunk Web

Browser opens automatically to:

http://localhost:8000

If it does not open automatically open any browser and go to:

http://localhost:8000

---

### Step 7 — First Login

1. Enter username: admin
2. Enter the password you set during installation
3. Click Sign In
4. Splunk Web dashboard loads

---

## Post-Install Verification

After logging in verify these are working:

| Check | How to Verify | Result |
|---|---|---|
| Splunk Web accessible | Open http://localhost:8000 | Loads correctly |
| Admin login works | Login with admin credentials | Logged in |
| Search works | Go to Search and Reporting — type index=* | Returns results |
| Splunk service running | Open Task Manager — Services — SplunkD | Running |

---

## Splunk Service Management

Start Splunk service manually if it stops:

Option 1 — Command Prompt (Run as Administrator)

cd "C:\Program Files\Splunk\bin"
splunk start

Stop Splunk:

splunk stop

Restart Splunk:

splunk restart

Option 2 — Windows Services

1. Press Windows + R — type services.msc
2. Find SplunkD
3. Right click — Start / Stop / Restart

---

## Key URLs After Installation

| URL | Purpose |
|---|---|
| http://localhost:8000 | Splunk Web — main interface |
| http://localhost:8000/en-US/app/search | Search and Reporting |
| http://localhost:8000/en-US/manager/search/data/inputs | Data Inputs |
| http://localhost:8000/en-US/app/search/dashboards | Dashboards |

---

## Splunk Directory Structure

C:\Program Files\Splunk\
|
|-- bin\                 — Splunk executables and CLI tools
|-- etc\                 — Configuration files
|   |-- apps\            — Installed apps and add-ons
|   |-- system\          — System configuration
|-- var\
|   |-- log\splunk\      — Splunk internal logs
|-- share\               — Web assets

---

## Core Splunk Concepts Learned During Install

| Concept | What It Means |
|---|---|
| Index | Where Splunk stores data — like a database table |
| Sourcetype | The format of the data — Windows logs, syslog, CSV etc |
| Host | The machine that generated the log |
| SplunkD | The Splunk daemon — the background service that runs everything |
| Splunk Web | The browser interface at localhost:8000 |
| Search and Reporting | The main app where you write SPL queries |

---

## First Queries After Install

Verify data is ingesting correctly:

Check all indexes:
index=*

Count events by sourcetype:
index=* | stats count by sourcetype

Check internal Splunk logs:
index=_internal | stats count by sourcetype

---

## What I Learned From This Install

- Splunk Enterprise installs cleanly on Windows 11 with no issues
- The free version gives full SPL access with 500MB daily ingest limit
- Splunk runs as a Windows service (SplunkD) in the background
- All interaction happens through the browser at localhost:8000
- The install itself is straightforward — the real learning starts with log ingestion

---

## Next Step

Add Windows logs, Apache logs, and sample data — day15-notes.md
