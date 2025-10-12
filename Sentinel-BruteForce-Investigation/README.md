# Sentinel Brute Force Investigation 

This project is part of the **Microsoft 30-Day SOC Analyst Challenge** by MyDFIR.  
The focus was to detect and investigate brute-force attacks using Microsoft Sentinel.

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
During this challenge I investigated a spike in failed logons (Event ID 4625) that suggested brute-force behaviour.  

This investigation took place during the transition of Microsoft Sentinel from the Azure portal to the Microsoft Defender portal.  
The navigation and feature layout were still being updated at that time, which made some actions (like creating rules or incidents) slightly inconsistent between the two environments.  
Working through this transition provided valuable experience in adapting to platform changes and navigating between the 2 platforms

---

## Tools Used
- Microsoft Sentinel  
- Microsoft Defender  
- Azure Log Analytics  
- Kusto Query Language (KQL)  
- Windows Security Event Logs  

---

## What I Did
1. Alert fired — Microsoft Sentinel triggered an alert showing multiple failed logons (Event ID 4625) within a short period.  
2. Initial triage — Opened the corresponding incident in the Microsoft Defender portal to review affected accounts, timestamps, and hosts.  
3. Data collection — Queried the `SecurityEvent_CL` table in Sentinel to extract logon activity (both 4624 and 4625 events).  
4. Account analysis — While in the Sentinel Logs workspace, ran the Failed vs Successful Logons KQL query to identify which accounts had the most failed attempts.  
5. Host analysis — Ran a second KQL query to determine which hosts were most targeted by these failed logons.  
6. Incident creation — Confirmed brute-force behaviour and created a manual incident in Sentinel for tracking and documentation.  
7. Analytics rule creation — Built a Scheduled Query Analytics Rule to automatically detect and raise future brute-force attempts using the same pattern.  
8. Reporting — Compiled all findings, evidence, and recommendations into a structured investigation report (`Brute-Force-Investigation_Report.md` and `.docx`).  
9. Dashboard creation — Designed a Sentinel workbook (dashboard) to visualise failed logons, account trends, and host activity for presentation and monitoring.

---

## Key Findings
- Brute-force pattern confirmed: repeated NTLM authentication attempts within one minute.  
- Main targets: `\ADMINISTRATOR`, `\admin`, `\administrator`.  
- Affected hosts: `SOC-FW-RDP`, `SHIR-Hive`.  
- No successful logons (4624) observed.

---

## Outcome
- Demonstrated ability to detect, analyse and report brute-force activity in Sentinel.   
- Created a clear report and supporting evidence screenshots for my portfolio.  
- Gained hands on experience with the early stages of Sentinel in the the Defender portal.

---

## Folder Structure

| Folder | Description |
|--------|--------------|
| **KQL/** | Detection queries used for analysis |
| **Reports/** | Investigation report (.md and .docx) |
| **Screenshots/** | Query results, bookmark, and incident evidence |

---

## Quick Links
- 📜 [Investigation Report (Markdown)](./Reports/Brute-Force-Investigation_Report.md)  
- 🧠 [KQL Queries](./KQL/)  
  - [Failed vs Successful Logons](./KQL/failed_vs_successful_logons.kql)  
  - [Failed Logons by Host](./KQL/failed-logons-by-host.kql)  
- 🖼️ [Screenshots](./Screenshots/)  
  - [Bookmark Creation](./Screenshots/create-sentinel-bookmark.PNG)  
  - [Incident Wizard](./Screenshots/create-incident-wizard.PNG)  
  - [Failed Logons by Host](./Screenshots/failed-logons-by-host.PNG)  
  - [Failed vs Successful Logons](./Screenshots/failed_vs_successful_logons.PNG)  

---

*This project forms part of my ongoing SOC analyst journey through the MyDFIR community.*
