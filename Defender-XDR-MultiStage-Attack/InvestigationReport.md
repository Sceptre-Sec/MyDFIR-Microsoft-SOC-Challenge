Investigation Report
Summary

On 2025-11-25 14:26:38 UTC, Microsoft Defender XDR detected suspicious activity on Jenny Smith’s device including recon commands, unrestricted execution policy and a process to dump LSASS in an attempt to steal credentials.

Reviewing Entra ID sign-in logs revealed multiple failed password attempts followed by successful logins from two different countries within minutes. This was impossible travel, confirming that Jenny’s credentials were compromised.

Looking further, Jenny’s mailbox revealed a phishing email containing a malicious link to a fake login page. Jenny clicked the link and most likely entered her Microsoft 365 password.

Because LSASS dumping was observed, lateral movement was suspected. On Bob Smith’s device, the timeline showed that the attacker accessed a sensitive document (IT-Admin-Notes.txt) and created a copy in the Windows\Temp directory. Soon after, the timeline showed suspicious patterns in AMSI content associated with 'Deobfuscate/Decode Files or Information' technique for the preparing of file exfiltration.

The final exfiltration step was not visible in defender but it is reasonable to conclude the sensitive data was successfully exfiltrated.

WHO
First Victim: Jenny Smith
Second Victim: Bob Smith
Attacker: UK/USA Locations. IP Address: 51.89.232.10, 51.195.242.230, 51.81.245.239
Hosts Involved: mydfir-sceptre- (initial compromise), mydfir-sceptre2
 (lateral movement & file exfiltration)

WHAT
Jenny’s Microsoft 365 credentials were stolen via phishing.
Attacker logged into her account from two countries (impossible travel).
Recon, PowerShell execution and an LSASS dump attempt were performed.
Lateral Movement to Bob’s device and accessed IT-Admin-Notes.txt.
A copy (export.txt) was created in Windows\Temp.
Data was encoded in Base64 and exfiltrated.

WHEN 
2025-11-25
Phishing email clicked: 12:51 UTC
Failed sign-in attempts: 13:50 UTC
Successful login in London: 13:51 UTC
Impossible travel sign-in from Oregon,US: 13:51 UTC
Recon on Jenny-VM: 14:26 UTC
LSASS dump attempt: 14:26 UTC
File accessed + copied on Bob-VM: 16:18 UTC
Exfiltration: 16:21 UTC

WHERE
Identity: Jenny Entra ID sign-in logs
Endpoints: Jenny’s and Bob’s Windows 11 Machines 

WHY
Jenny entered her password into a phishing website allowing the attacker to authenticate as Jenny. The attacker used the stolen credentials to explore the environment, steal more credentials and extract a sensitive file.

HOW
The attacker used:
Phishing 
Remote Login
LSASS Dump
Lateral movement 
Accessed Sensitive Files
Base64 encoding for exfiltration

Indicators of Compromise (IoCs)
Emails / Domains
Sender: misterfinancelondon@outlook.com
Phishing site: MisterFinanceLondon.com

IP Addresses (attacker)
UK IP: 51.89.232.10, 51.195.242.230 USA IP: 51.81.245.239

Files
Sensitive: C:\Users\BobSmith\Documents\IT-Admin-Notes.txt
File Copy: C:\Windows\Temp\export.txt

Processes / Commands 
whoami, hostname
powershell -ExecutionPolicy Unrestricted
rundll32.exe comsvcs.dll MiniDump

Recommendations
Enable MFA for all users to prevent credential theft abuse.
Phishing protection (Defender for Office 365, SafeLinks, SafeAttachments).
Harden endpoints by restricting PowerShell policies and enabling LSASS protection.
Review access to sensitive documents and apply least privilege controls.
Provide phishing awareness training to reduce future credential harvesting.
