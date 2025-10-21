# Microsoft Defender Anti-Phishing Policy Setup

**Analyst:** Sceptre  
**Date:** 21 October 2025  
**Time zone:** London (UTC+01:00)

---

## Step 1 – Create the policy
Created a new anti-phishing policy named **DFIR-Sceptre-AntiPhish**.

[Policy Name](../Screenshots/PolicyName.PNG)

---

## Step 2 – Assign users, groups, and domains
Added the test mailbox `bob@socialgore.onmicrosoft.com` under *Users, Groups and Domains*.

[Users, Groups and Domains](../Screenshots/Users_Groups_Domains.PNG)

---

## Step 3 – Configure phishing threshold and protection
Set threshold to **Standard protection**, enabled **user impersonation** and **domain impersonation** checks.

[Phishing Threshold Protection](../Screenshots/Phishing_Theshold_Protection.PNG)

---

## Step 4 – Define actions
Chose **Move message to Junk Email folder** as the default action.

[Actions](../Screenshots/Actions.PNG)



---

## Step 5 – Review and publish
Reviewed all settings before creation:

- Policy name: **DFIR-Sceptre-AntiPhish**  
- Target: Bob test mailbox  
- Action: Move to Junk  
- Quarantine policy: DefaultFullAccessPolicy  
- Spoof protection: Enabled  

Clicked **Create Policy** to publish.

---

## Step 6 – Validate activity in Explorer
Opened **Microsoft 365 Defender → Email & Collaboration → Explorer**.  
Switched to the **URL clicks** tab and confirmed user activity on the domain `neon-lab.test`.

[Explorer URL Clicks](../Screenshots/Explorer_URL_Clicks.PNG)


---

## Key takeaway
During testing, Defender’s built-in anti-spam policies initially quarantined my simulated phishing emails before my rule triggered.  
I learned that **policy priority order** determines which rule evaluates first.  
After moving **DFIR-Sceptre-AntiPhish** to **Priority 1**, my custom policy ran before Microsoft’s defaults and worked correctly.

---

*Documented for the MyDFIR 30-Day SOC Challenge – showing configuration, validation, and lessons learned.*
