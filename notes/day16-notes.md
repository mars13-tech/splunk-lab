# Day 16 Notes — SPL Basics & Analyst Mindset

## Objective

Learn the fundamentals of SPL (Search Processing Language) and develop the mindset of a SOC analyst by analyzing logs, identifying patterns, and understanding attacker behavior.

---

# Core SPL Pipeline Structure

```spl
index=* keyword
| command
| command
```

SPL workflow:

1. Search logs
2. Filter data
3. Analyze behavior
4. Present evidence

---

# Important SPL Commands

| Command   | Purpose                            |
| --------- | ---------------------------------- |
| search    | Find logs and evidence             |
| stats     | Summarize behavior                 |
| table     | Display important fields only      |
| top       | Find most common values            |
| rare      | Identify uncommon/anomalous values |
| timechart | Visualize activity over time       |

---

# Query 1 — Show All Logs

```spl
index=*
```

Purpose:

* Verify data ingestion
* Check available logs
* Confirm Splunk visibility

Important fields:

* `_time`
* `host`
* `source`
* `sourcetype`
* `_raw`

---

# Query 2 — Failed Logins

```spl
index=* EventCode=4625
```

Purpose:

* Detect failed authentication attempts
* Identify brute-force activity
* Investigate suspicious login failures

Analyst Questions:

* Which account failed most?
* Which IP generated failures?
* Was the activity repetitive?

---

# Query 3 — Successful Logins

```spl
index=* EventCode=4624
```

Purpose:

* Identify successful logons
* Correlate successful logins after failures

Threat Hunting Idea:

```text
4625 → 4624
```

Possible indication of account compromise.

---

# Query 4 — Count Failed Logins

```spl
index=* EventCode=4625
| stats count
```

Purpose:

* Count authentication failures
* Measure attack volume

---

# Query 5 — Failed Logins by User

```spl
index=* EventCode=4625
| stats count by Account_Name
```

Purpose:

* Identify targeted users
* Detect brute-force targeting

Modified Version:

```spl
index=* EventCode=4625
| stats count by Account_Name
| sort - count
```

---

# Query 6 — Clean Evidence Table

```spl
index=* EventCode=4625
| table _time Account_Name host src_ip
```

Purpose:

* Reduce noise
* Present investigation evidence clearly

---

# Query 7 — Top Source IPs

```spl
index=*
| top src_ip
```

Purpose:

* Identify noisy IPs
* Detect scanners or brute-force sources

Modified Version:

```spl
index=* EventCode=4625
| top src_ip
```

---

# Query 8 — Rare Processes

```spl
index=*
| rare process_name
```

Purpose:

* Identify uncommon or suspicious processes
* Support threat hunting

Threat Hunting Questions:

* Does the process belong on the system?
* Is it malware or LOLBin abuse?
* Why is it rare?

---

# Query 9 — Login Activity Over Time

```spl
index=* EventCode=4625
| timechart count
```

Purpose:

* Visualize failed login spikes
* Detect brute-force patterns

Modified Version:

```spl
index=* EventCode=4625
| timechart span=5m count
```

Possible Patterns:

* Sudden spike → brute force
* Regular intervals → beaconing
* Slow consistent attempts → password spraying

---

# Query 10 — Top Event Codes

```spl
index=*
| top EventCode
```

Purpose:

* Understand dominant log activity
* Learn environment behavior

---

# Key Analyst Concepts

## 1. Visibility

Without logs, defenders are blind.

SOC investigations always begin with visibility.

---

## 2. Filtering

Filtering removes noise and isolates suspicious activity.

Example:

```spl
EventCode=4625
```

---

## 3. Aggregation

`stats` transforms thousands of logs into behavior summaries.

Example:

```spl
| stats count by user
```

---

## 4. Frequency Analysis

`top` identifies dominant behavior patterns.

Example:

```spl
| top src_ip
```

---

## 5. Anomaly Detection

`rare` helps identify unusual activity.

Example:

```spl
| rare process_name
```

---

## 6. Timeline Analysis

`timechart` reconstructs attack timelines.

Example:

```spl
| timechart count
```

---

# SOC Analyst Investigation Questions

For every query ask:

| Question            | Purpose                   |
| ------------------- | ------------------------- |
| What happened?      | Identify activity         |
| Who did it?         | Identify user or attacker |
| When did it happen? | Build timeline            |
| From where?         | Source analysis           |
| Is it normal?       | Detect anomalies          |

---

# Analyst vs Beginner Mindset

## Beginner

* Memorizes SPL syntax
* Reads logs individually
* Runs queries mechanically

## Analyst

* Understands attacker behavior
* Detects patterns
* Investigates anomalies
* Correlates events into attack stories

---

# Most Important Lesson

SPL is not just about searching logs.

SPL is used to:

* detect attacker behavior
* identify anomalies
* reconstruct timelines
* investigate incidents
* perform threat hunting

---

# Blue-Team Mentality Learned Today

```text
Logs → Patterns → Evidence → Conclusion
```

Strong analysts think statistically and behaviorally instead of focusing on single events.

---

# Day 16 Outcome

Completed:

* SPL Fundamentals
* search command
* stats command
* table command
* top command
* rare command
* timechart command
* 10 beginner SPL queries
* analyst investigation mindset

---

# End-of-Day Reflection

Today I learned:

* how SOC analysts search logs
* how to summarize large datasets
* how to identify anomalies
* how to think in attack timelines
* how to analyze attacker behavior using SPL

This is the foundation of:

* SOC analysis
* threat hunting
* detection engineering
* incident response
* blue-team operations
