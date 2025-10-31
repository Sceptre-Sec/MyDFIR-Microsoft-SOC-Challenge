# KQL Queries Template and Examples

This file contains the KQL template I use for hunting in Defender for Endpoint and four example queries I ran during the T1059.001 investigation. Use this as a starting point and change the time window, device name and search terms to match your case.

## General template
1. Pick the right table for the behaviour you want to hunt (DeviceProcessEvents, DeviceFileEvents, DeviceNetworkEvents, DeviceImageLoadEvents).
2. Scope the time window to the approximate activity window using `between (datetime(...) .. datetime(...))`.
3. Scope to the device or account if you know it using `where DeviceName contains "..." or AccountName contains "..."`.
4. Filter on file names, command line text or other indicators using `has`, `has_any` or `in~`.
5. Project the useful columns and order the results.

Template structure
```kql
<TableName>
| where Timestamp between (datetime(YYYY-MM-DD HH:MM:SS) .. datetime(YYYY-MM-DD HH:MM:SS))
| where DeviceName contains "my-device-name" or AccountName contains "JennySmith"
| where <field> has_any ("term1","term2","term3")
| project Timestamp, DeviceName, AccountName, FileName, ProcessCommandLine, InitiatingProcessFileName, InitiatingProcessCommandLine, ReportId
| order by Timestamp asc
```

## Query 1 — Confirm PowerShell execution
Purpose: show the actual PowerShell process creation events that contain IEX or DownloadString and confirm the parent process.

```kql
DeviceProcessEvents
| where Timestamp between (datetime(2025-10-27 15:50:00) .. datetime(2025-10-27 16:10:00))
| where DeviceName contains "mydfir-sceptre" or AccountName contains "JennySmith"
| where FileName in~ ("powershell.exe", "pwsh.exe")
| where ProcessCommandLine has_any ("IEX", "DownloadString", "github", "atomic-red-team", "PowerSploit")
| project Timestamp, DeviceName, AccountName, ProcessId, InitiatingProcessFileName, InitiatingProcessId, FileName, ProcessCommandLine, FolderPath, ReportId
| order by Timestamp asc
```

## Query 2 — Look for Mimikatz and impact checks
Purpose: determine whether any `Invoke-Mimikatz` or LSASS dump activity occurred.

```kql
DeviceProcessEvents
| where Timestamp between (datetime(2025-10-27 15:50:00) .. datetime(2025-10-27 16:20:00))
| where DeviceName contains "mydfir-sceptre" or AccountName contains "JennySmith"
| where ProcessCommandLine has_any ("Invoke-Mimikatz","Invoke-Mimikatz -DumpCreds","mimikatz","sekurlsa::logonpasswords","sekurlsa::minidump")
| project Timestamp, DeviceName, AccountName, ProcessId, InitiatingProcessFileName, InitiatingProcessId, FileName, ProcessCommandLine, ReportId
| order by Timestamp asc
```

## Query 3 — Look for spread across the estate
Purpose: run an environment wide search for the same command patterns to see if other devices show the same behaviour.

```kql
DeviceProcessEvents
| where Timestamp between (datetime(2025-10-27 15:50:00) .. datetime(2025-10-27 16:30:00))
| where FileName in~ ("powershell.exe", "pwsh.exe")
| where ProcessCommandLine has_any ("IEX", "DownloadString", "github", "atomic-red-team", "PowerSploit")
| summarize DeviceCount=dcount(DeviceName), Devices=make_set(DeviceName) by ProcessCommandLine
| order by DeviceCount desc
```

## Query 4 — Look for persistence
Purpose: check for evidence of scheduled tasks, registry run keys, services or new users created in the window.

```kql
DeviceProcessEvents
| where Timestamp between (datetime(2025-10-27 15:50:00) .. datetime(2025-10-27 16:20:00))
| where DeviceName contains "mydfir-sceptre" or AccountName contains "JennySmith"
| where ProcessCommandLine has_any ("schtasks", "reg add", "New-LocalUser", "net user", "net localgroup", "powershell.exe Set-ItemProperty", "Set-Service", "schtasks /create")
| project Timestamp, DeviceName, AccountName, FileName, ProcessCommandLine, FolderPath, InitiatingProcessFileName
| order by Timestamp asc
```



Made by Sceptre — beginner SOC analyst
