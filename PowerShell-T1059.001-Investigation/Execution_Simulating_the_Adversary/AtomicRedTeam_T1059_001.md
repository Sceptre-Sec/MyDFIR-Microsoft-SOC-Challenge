# Atomic Red Team T1059.001 — quick install and run

This file shows the exact commands I used to install the Atomic Red Team helpers and run the PowerShell test for T1059.001 (Command and scripting interpreter PowerShell). Reference for the technique on the MITRE ATT&CK site: https://attack.mitre.org/techniques/T1059/001/

> Note: Only run these in an isolated lab environment with Defender configured for testing. Do not run on production systems.

## Install and run

1. Install the helper modules for the current user
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

## Notes and troubleshooting

- The `Invoke-AtomicTest` harness runs a number of subtests. Some subtests rely on helper binaries located under `atomics/T1059.001/bin`. If those binaries are not present you will see errors such as `CommandNotFoundException` for SOAPHound or similar. This is normal if you have not downloaded extra helper binaries.
- If you only want the simple download and execute behaviour that produces the `cmd -> powershell -> IEX DownloadString` telemetry used in my hunt, run the direct command that downloads `test.ps1` from the Atomic Red Team repo and executes it with `IEX`. That will produce the same PowerShell execution pattern without needing the extra binaries.
- Always run Atomic Red Team tests in an isolated lab and make sure endpoint protections are set appropriately for the exercise.

If you want, I can add the direct IEX command example and the short explanation about what to expect in Defender telemetry.
