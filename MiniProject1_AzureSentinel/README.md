# Mini Project 1 — Azure Sentinel Lab  

## 🎯 Purpose  
This project documents my hands-on experience building detections and dashboards in **Microsoft Azure Sentinel** as part of the MyDFIR 30-Day Microsoft SOC Challenge.  

I wanted to simulate a SOC analyst workflow — building detections, visualising logs, and writing an investigation report.  

---

## 🧠 Project Overview  
**Tools Used:**  
- Microsoft Sentinel  
- Azure Virtual Machines (Windows 11)  
- Log Analytics Workspace  
- KQL (Kusto Query Language)

**Skills Practiced:**  
- Creating dashboards  
- Writing KQL queries  
- Building custom alerts  
- Documenting incidents  

---

## 🧩 Steps Taken  
1. Created an Azure account and configured billing alerts  
2. Deployed a Windows 11 virtual machine  
3. Connected logs to Sentinel  
4. Built a dashboard with **bar, pie, and table** visuals  
5. Ran a KQL query for failed logons  
6. Created a test alert and a manual incident  

```kql
SecurityEvent_CL
| where EventID_s == "4625"
| summarize FailedLogons = count() by Account_s
| where FailedLogons >= 1000
