# PowerShell attack investigation — T1059.001 (Sceptre)

## Overview
This project documents how I brought a Windows 11 VM online with Microsoft Defender for Endpoint, ran an Atomic Red Team test that simulated a PowerShell based attack, hunted the resulting telemetry with KQL, and produced an investigation report. The aim was to practise real world SOC skills while learning how Defender, Intune and Advanced Hunting fit together.

## 1. Setup and Defender configuration
I prepared the environment to get meaningful telemetry and to make sure Defender could detect and block the test activity. Key steps included:
- Enabling Defender for Endpoint features in the portal: EDR in block mode, tamper protection, web content filtering, Live Response and custom network indicators.
- Turning on Intune and joining the device to Microsoft Entra ID.
- Creating endpoint security policies and Attack Surface Reduction rules in Intune to block common office child processes and credential theft techniques.

## 2. Onboarding Jenny
I used the streamlined onboarding package for Windows 11 and the local script method for small deployments. Steps I followed:
- Downloaded the onboarding package from the Defender portal.
- RDP into Jenny, extracted the package and ran the onboarding command as administrator.
- Verified the device appeared in the Defender portal and that Intune reported the device as enrolled and compliant.
This brought Jenny online as an endpoint I could safely test against.

## 3. Execution — Running Atomic Red Team
To simulate an attacker I ran Atomic Red Team technique T1059.001 which uses PowerShell to download and run scripts from the internet. The test I used attempted to fetch and invoke a script from GitHub using IEX and DownloadString. Defender detected the activity and blocked several payloads including a Mimikatz attempt.

## 4. Detection and Hunting
After triggering the Atomic Red Team tests I switched into a hunting mindset. The investigation steps were:
- Review alerts and process trees in the Defender portal to understand the sequence of events.
- Answer five starter questions I prepared: what connects the alerts, which alert came first, what actually ran, who did it, and what happened next.
- Run targeted KQL queries against DeviceProcessEvents to confirm execution, check for credential dumping, look for spread across other devices and search for any signs of persistence.

## 5. Findings and Investigation Report
The short story of what happened:
- An interactive remote session under AzureAD\JennySmith spawned cmd.exe which in turn launched a PowerShell process that used DownloadString and IEX to fetch remote code from GitHub.
- Defender generated multiple alerts. One showed the suspicious PowerShell command text, another confirmed the process executed, and a third showed a blocked Mimikatz attempt labelled Trojan:PowerShell/Mimikatz.A.
- Advanced Hunting confirmed the PowerShell execution event but did not return evidence that Invoke Mimikatz actually ran. That is because Defender blocked the payload before the inner script executed, so telemetry shows the attempted download and execution but not the inner malicious commands.
- Spread checks showed only Jenny was involved. Persistence searches returned nothing because the Atomic test did not attempt persistence and Defender prevented follow up activity.

