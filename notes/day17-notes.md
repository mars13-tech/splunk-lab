# Day 17 – SPL Intermediate (eval, dedup, rex, transaction, lookup)

## Objective

Learn intermediate Splunk SPL commands used by SOC analysts for:

* Event enrichment
* Noise reduction
* Data extraction
* Timeline reconstruction
* Threat intelligence enrichment

---

# 1. eval

## Purpose

Creates new fields or modifies existing fields during a search.

## Syntax

```spl
| eval field=value
```

## Example

```spl
index=* EventCode=4625
| eval severity="High"
| table _time Account_Name severity
```

## SOC Use Cases

* Assign alert severity
* Create risk scores
* Categorize events
* Normalize log fields

### Example

```spl
index=*
| eval login_status=if(EventCode=4624,"SUCCESS","FAILURE")
```

### Analyst Mindset

Ask:

> What information would help me make a better decision?

---

# 2. dedup

## Purpose

Removes duplicate events based on a specific field.

## Syntax

```spl
| dedup field_name
```

## Example

```spl
index=*
| dedup src_ip
| table src_ip
```

## SOC Use Cases

* Unique attacker IPs
* Unique usernames
* Unique hosts
* Unique malware hashes

### Example

```spl
index=*
| dedup Account_Name
| table Account_Name
```

### Analyst Mindset

Ask:

> What duplicates are hiding the real story?

---

# 3. rex

## Purpose

Extracts hidden information from raw logs using regular expressions.

## Syntax

```spl
| rex field=_raw "regex"
```

## Extract IPv4 Address

```spl
index=*
| rex field=_raw "(?<ip>\d+\.\d+\.\d+\.\d+)"
| table ip
```

## Extract Username

```spl
index=*
| rex field=_raw "Account Name:\s+(?<username>\w+)"
| table username
```

## SOC Use Cases

* Extract usernames
* Extract IP addresses
* Extract URLs
* Extract hashes

### Analyst Mindset

Ask:

> What useful evidence is hidden inside the raw log?

---

# 4. transaction

## Purpose

Groups related events into sessions or timelines.

## Syntax

```spl
| transaction field maxspan=time
```

## Example

```spl
index=*
| transaction Account_Name maxspan=10m
| table Account_Name eventcount duration
```

## Example Output

| Account_Name | eventcount | duration |
| ------------ | ---------- | -------- |
| vvman        | 165        | 592      |
| SYSTEM       | 11         | 593      |

### Meaning

* eventcount = number of grouped events
* duration = total session duration in seconds

## SOC Use Cases

* Login session analysis
* Failed-to-successful login tracking
* Attack timeline reconstruction
* Lateral movement investigations

### Analyst Mindset

Ask:

> Which events belong to the same story?

---

# 5. lookup

## Purpose

Enriches logs using external CSV data.

## Syntax

```spl
| lookup file.csv field OUTPUT result
```

## Example

```spl
index=*
| lookup threat_feed.csv ip AS src_ip OUTPUT threat
```

## Sample Lookup File

| ip      | threat     |
| ------- | ---------- |
| 8.8.8.8 | Malware C2 |
| 1.2.3.4 | Phishing   |

## Result

| src_ip  | threat     |
| ------- | ---------- |
| 8.8.8.8 | Malware C2 |

## SOC Use Cases

* Threat intelligence enrichment
* IOC matching
* Asset inventory enrichment
* User context enrichment

### Analyst Mindset

Ask:

> Can I enrich this alert with external intelligence?

---

# Hands-On Queries

## Query 1 – Create Severity Field

```spl
index=* EventCode=4625
| eval severity="Medium"
| table _time Account_Name severity
```

## Query 2 – High Risk Login Failures

```spl
index=* EventCode=4625
| eval severity="High"
| stats count by Account_Name severity
```

## Query 3 – Unique Source IPs

```spl
index=*
| dedup src_ip
| table src_ip
```

## Query 4 – Unique Users

```spl
index=*
| dedup Account_Name
| table Account_Name
```

## Query 5 – Extract Username

```spl
index=*
| rex field=_raw "Account Name:\s+(?<username>\w+)"
| table username
```

## Query 6 – Extract IPv4 Address

```spl
index=*
| rex field=_raw "(?<ip>\d+\.\d+\.\d+\.\d+)"
| table ip
```

## Query 7 – Login Session Tracking

```spl
index=*
| transaction Account_Name maxspan=10m
| table Account_Name eventcount duration
```

## Query 8 – Failed Then Successful Login

```spl
index=* (EventCode=4624 OR EventCode=4625)
| transaction Account_Name maxspan=15m
```

## Query 9 – Count Unique Hosts

```spl
index=*
| stats dc(host)
```

## Query 10 – Top Users

```spl
index=*
| stats count by Account_Name
| sort -count
```

---

# Key Takeaways

| Command     | Purpose          |
| ----------- | ---------------- |
| eval        | Add meaning      |
| dedup       | Remove noise     |
| rex         | Extract evidence |
| transaction | Build timelines  |
| lookup      | Add intelligence |
| stats       | Count activity   |

---

# Day 17 Outcome

✅ Learned eval, dedup, rex, transaction, and lookup

✅ Practiced 10 SPL queries

✅ Built login session timelines using transaction

✅ Extracted data using rex

✅ Removed duplicate data using dedup

✅ Understood lookup-based enrichment

✅ Developed SOC investigation thinking

---

# SOC Analyst Summary

A SOC analyst uses:

* eval to add context
* dedup to reduce noise
* rex to extract indicators
* transaction to reconstruct timelines
* lookup to enrich alerts

These commands form the foundation of Splunk-based investigations and threat hunting.
