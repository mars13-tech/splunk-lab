# Splunk Enterprise Installation on Windows 11

**Author:** Karthikeyan  
**Platform:** Windows 11  
**Splunk Version:** Splunk Enterprise 10.4.0 (Free Trial)  
**Install Time:** ~10 Minutes  
**Status:** Successfully Installed and Running  

---

# Overview

Splunk Enterprise Free allows users to ingest up to **500 MB of data per day** without purchasing a license.
It provides full SPL (Search Processing Language) functionality, making it ideal for:

* SOC Analyst Labs
* Detection Engineering Practice
* Log Analysis
* Threat Hunting
* SIEM Training

---

# Download Splunk Enterprise

1. Visit the official Splunk download page:

   * https://www.splunk.com/en_us/download/splunk-enterprise.html
2. Create a free Splunk account
3. Select **Windows**
4. Download:

   * `Splunk Enterprise 10.4.0 (.msi)`
5. Installer size:

   * Approximately 450 MB

---

# Installation Steps

## Step 1 — Run the Installer

1. Double-click the downloaded `.msi` installer
2. Accept the UAC prompt
3. Splunk Setup Wizard opens

---

## Step 2 — Accept License Agreement

1. Accept the License Agreement
2. Select:

   * **Splunk Enterprise Free License**
3. Click **Next**

---

## Step 3 — Configure Admin Credentials

Create administrator credentials:

| Setting  | Example         |
| -------- | --------------- |
| Username | admin           |
| Password | Strong Password |

> **Important:** Save the password securely. Recovery is difficult without reinstalling.

---

## Step 4 — Choose Install Location

Default installation path:

```powershell
C:\Program Files\Splunk
```

Recommended:

* Keep the default path unless customization is required

Click:

* **Install**

---

## Step 5 — Complete Installation

1. Installation takes approximately 10 minutes
2. Enable:

   * `Launch browser with Splunk Enterprise`
3. Click:

   * **Finish**

---

## Step 6 — Access Splunk Web

Open Splunk Web:

```text
http://localhost:8000
```

If the browser does not open automatically:

1. Open any browser
2. Navigate to:

   * `http://localhost:8000`

---

## Step 7 — First Login

Login using the credentials created earlier.

| Field    | Value         |
| -------- | ------------- |
| Username | admin         |
| Password | Your Password |

After login:

* Splunk Web dashboard loads successfully

---

# Post-Installation Verification

| Check                | Verification Method      | Expected Result  |
| -------------------- | ------------------------ | ---------------- |
| Splunk Web Access    | Open localhost:8000      | Dashboard loads  |
| Admin Authentication | Login with admin account | Login successful |
| Search Functionality | Run `index=*`            | Events returned  |
| Splunk Service       | Check `SplunkD` service  | Running          |

---

# Managing Splunk Services

## Using Command Prompt (Administrator)

Navigate to the Splunk binary directory:

```powershell
cd "C:\Program Files\Splunk\bin"
```

### Start Splunk

```powershell
splunk start
```

### Stop Splunk

```powershell
splunk stop
```

### Restart Splunk

```powershell
splunk restart
```

---

## Using Windows Services

1. Press:

   * `Windows + R`
2. Run:

   * `services.msc`
3. Locate:

   * `SplunkD`
4. Right-click:

   * Start / Stop / Restart

---

# Important Splunk URLs

| URL                                                      | Purpose              |
| -------------------------------------------------------- | -------------------- |
| `http://localhost:8000`                                  | Splunk Web Interface |
| `http://localhost:8000/en-US/app/search`                 | Search & Reporting   |
| `http://localhost:8000/en-US/manager/search/data/inputs` | Data Inputs          |
| `http://localhost:8000/en-US/app/search/dashboards`      | Dashboards           |

---

# Splunk Directory Structure

```text
C:\Program Files\Splunk\
│
├── bin\                  # Splunk executables and CLI tools
├── etc\
│   ├── apps\             # Installed apps and add-ons
│   └── system\           # System configurations
├── var\
│   └── log\splunk\       # Internal Splunk logs
└── share\                # Web assets
```

---

# Core Splunk Concepts

| Concept            | Description                       |
| ------------------ | --------------------------------- |
| Index              | Location where Splunk stores data |
| Sourcetype         | Format/type of ingested logs      |
| Host               | Device generating the logs        |
| SplunkD            | Main Splunk background service    |
| Splunk Web         | Browser interface for Splunk      |
| Search & Reporting | Primary SPL workspace             |

---

# First SPL Queries

## View All Indexed Data

```spl
index=*
```

---

## Count Events by Sourcetype

```spl
index=* | stats count by sourcetype
```

---

## View Internal Splunk Logs

```spl
index=_internal | stats count by sourcetype
```

---

# Key Takeaways

* Splunk Enterprise installs smoothly on Windows 11
* Free version includes complete SPL functionality
* Splunk runs as a background Windows service (`SplunkD`)
* Main interaction occurs through the browser interface
* Installation is simple — log ingestion and analysis are the real learning areas

---

# Skills Practiced

* Splunk Installation
* Windows Service Management
* SPL Basics
* SIEM Fundamentals
* Log Analysis
* SOC Lab Setup

---

# Author

**Karthikeyan**
Blue Team | SOC Analyst in the Making

GitHub:

* https://github.com/mars13-tech
