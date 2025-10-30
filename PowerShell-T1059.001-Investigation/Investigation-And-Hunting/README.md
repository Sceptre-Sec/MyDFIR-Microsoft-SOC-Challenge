# Investigation and hunting

This folder contains the core investigation work from the PowerShell T1059.001 simulation. It brings together the alerts, process trees, KQL hunts and the evidence I collected while treating the Atomic Red Team exercise like a real incident.

## What is in this folder
- `README.md` this file, overview and quick links.
- `KQL-queries.md` saved queries used for confirmation, impact, spread and persistence checks.
- `Alerts_and_Timelines/` screenshots and exported timelines for Alert 1, Alert 2 and Alert 3.
- `ProcessTrees/` full and trimmed process tree images used to establish parent child relationships.
- `Evidence_Table.md` a short index linking screenshots to queries and timestamps.
- `forensic_actions.md` suggested next steps if anything had been confirmed.

## Quick summary of findings
- The activity originated from an interactive remote session for user AzureAD\JennySmith and not from a service or scheduled task.
- `cmd.exe` spawned a `powershell.exe` process which used DownloadString plus IEX to fetch and attempt to execute scripts from GitHub.
- Defender generated three related alerts: suspicious PowerShell command, suspicious process executed PowerShell command, and a blocked Mimikatz credential theft attempt.
- KQL hunting confirmed the attempted PowerShell execution. There was no evidence that Invoke Mimikatz actually ran because Defender blocked the payload before the inner script executed.
- Spread checks showed the activity was limited to the single onboarded endpoint. Persistence checks returned no results.

## Practical notes for reviewers
- Start with the Alerts and Timelines to follow the high level story then open the ProcessTrees to confirm the parent process and timestamps.
- Use `KQL-queries.md` to reproduce the hunts. The queries follow a simple template: pick the right table, filter the time range, scope to device or account, search the command line and then project or summarise the most useful fields.
- Evidence_Table.md maps each screenshot to the exact KQL used and the defender ReportId where applicable.

## Next steps if this were real
1. Isolate the device if there was evidence of credential dumping or persistence.
2. Collect forensic artifacts memory and disk images from the machine before rebooting.
3. Rotate credentials for any accounts that may have been exposed.
4. Run environment wide hunts for the IEX DownloadString pattern and cross correlate with email and proxy logs to find the delivery vector.

## Where to find the KQL queries
See `KQL-queries.md` in this folder for the exact queries used in the investigation. If you want I can also save each query as a `.kql` file for import into Hunting notebooks.

---

Made by Sceptre — beginner SOC analyst tone, straightforward and practical. If you want this saved as `README.md` inside the folder I have already done that. Download it below.
