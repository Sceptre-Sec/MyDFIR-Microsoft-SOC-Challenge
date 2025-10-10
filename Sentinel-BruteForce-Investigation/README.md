# Sentinel Brute Force Investigation (Day 7)

This project is part of the **Microsoft 30-Day SOC Analyst Challenge** by MyDFIR.  
The focus was to detect, investigate and automate alerting for brute-force attacks using Microsoft Sentinel.

---

## Table of Contents
- [Overview](#overview)
- [Tools Used](#tools-used)
- [What I Did](#what-i-did)
- [Key Findings](#key-findings)
- [Outcome](#outcome)
- [Folder Structure](#folder-structure)
- [Quick Links](#quick-links)

---

## Overview

During this challenge, I investigated a spike in failed logons (Event ID 4625) that suggested brute-force behaviour.  
Using KQL, dashboards, and analytics rules, I explored how to identify these patterns, confirm the findings, and build automation to detect them in the future.

---

## Tools Used
- Microsoft Sentinel  
- Azure Log Analytics  
- Kusto Query Language (KQL)  
- Windows Security Event Logs  

---

## What I Did
1. Ran KQL queries to compare failed (4625) and successful (4624) logons.  
2. Identified administrator accounts targeted by thousands of failed NTLM logons.  
3. Created a **Sentinel bookmark** and opened a **manual incident**.  
4. Built an **Analytics Rule** that triggers an incident automatically when failed logons exceed a set threshold.  
5. Designed a **Sentinel Dashboard** to visualise activity trends and host targets.  
6. Wrote an investigation report summarising findings and recommendations.

---

## Key Findings
- Brute-force pattern confirmed: repeated NTLM authentication attempts within one minute.  
- Main targets: `\ADMINISTRATOR`, `\admin`, `\administrator`.  
- Affected hosts: `SOC-FW-RDP`, `SHIR-Hive`.  
- No successful logons observed (4624).  
- Evidence points to automated password spraying or brute-force activity.

---

## Outcome
- Confirmed the ability to detect and document brute-force behaviour in Sentinel.  
- Built a working analytics rule that creates incidents automatically.  
- Produced a reusable dashboard and KQL set for similar investigations.  

---

## Folder Structure

| Folder | Description |
|--------|--------------|
| **KQL/** | Detection queries used for analysis and alert creation |
| **Reports/** | Investigation report and reflection |
| **AnalyticsRule/** | Brute-force alert logic and exported JSON |
| **Dashboard/** | Sentinel workbook and related screenshots |
| **Screenshots/** | Query results, alert views, and incidents |

---

## Quick Links
- 📜 [Investigation Report](./Reports/Day7_Investigation_Report.md)  
- ⚙️ [Analytics Rule](./AnalyticsRule/BruteForce_Alert_Rule.md)  
- 📊 [Dashboard Overview](./Dashboard/Sentinel_BruteForce_Dashboard.md)  
- 🧠 [KQL Queries](./KQL/)  
- 🖼️ [Screenshots](./Screenshots/)  

---

*This project forms part of my ongoing SOC analyst journey through the MyDFIR community.*
