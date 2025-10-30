Initial setup — bringing Jenny online

This page documents how I prepared the Windows 11 VM (Jenny) so it would produce useful telemetry for the Atomic Red Team tests and so Defender could detect and block malicious activity.

Goals
- Onboard the VM to Microsoft Defender for Endpoint and Intune.
- Enable EDR features so runtime activity would be recorded and blocked where appropriate.
- Apply baseline endpoint security and Attack Surface Reduction rules to reduce noise and cover credential theft techniques.
- Make the VM reachable for RDP testing and lab work.

Defender portal settings I enabled
- EDR in block mode (Enable EDR in Block Mode).  
- Tamper protection.  
- Web content filtering.  
- Live Response.  
- Custom network indicators.  
- Mailbox and web protections as appropriate for lab telemetry.

Intune and device enrollment
- Logged into `intune.microsoft.com and confirmed Intune was turned on.  
- Under Enrolment, enabled automatic enrolment for Windows (All).  
- On Jenny (Windows 11 VM) I went to Settings > Access work or school and joined the device to Microsoft Entra ID.  
- Verified the device appeared in Intune as enrolled.

Onboarding Defender for Endpoint (Windows 11)
- Downloaded the streamlined onboarding package from the Defender portal. 
- RDP to Jenny, extracted the package and ran the onboarding script as administrator.   
- Confirmed the device showed up in the Defender portal.

Host and RDP convenience tweaks
- Edited `C:\Windows\System32\drivers\etc\hosts` to add the relevant DNS/host entries for RDP.

Endpoint security and ASR rules (Intune)
- In Intune → Endpoint Security enabled Microsoft managed endpoint configurations.  
- Created Attack Surface Reduction (ASR) rules and policies applied to all devices:
  - Block Office applications from spawning child processes.
  - Block credential theft behaviours and protect LSASS where supported.
  - Enable advanced ransomware protection.
- Created a policy to push these settings to enrolled devices and confirmed policy sync on Jenny.

Verification steps 
- Confirmed device appears in Defender portal and shows as onboarded.  
- Verified Intune policy sync and ASR settings applied on Jenny.  
- Performed a small benign test (eg a harmless PowerShell command) to confirm telemetry and Live Response functionality.

Screenshots
I took screenshots during each step to prove the onboarding and policy application moments. These images are stored in the `images/` folder for this section.

