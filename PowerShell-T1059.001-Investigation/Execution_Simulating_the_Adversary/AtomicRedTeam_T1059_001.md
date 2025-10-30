# Atomic Red Team T1059.001 — Quick Install and Run

This file shows the exact commands I used to install Atomic Red Team and run the PowerShell test for T1059.001 (Command and scripting interpreter PowerShell). Reference for the technique on the MITRE ATT&CK site: https://attack.mitre.org/techniques/T1059/001/



## Install and run

1. Install the modules for the current user
```powershell
Install-Module -Name invoke-atomicredteam,powershell-yaml -Scope CurrentUser
```

2. Download and install the Atomics
```powershell
IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing); Install-AtomicRedTeam -getAtomics
```

3. Invoke the specific atomic test for PowerShell execution
```powershell
Invoke-AtomicTest T1059.001
```



If you want, I can add the direct IEX command example and the short explanation about what to expect in Defender telemetry.
