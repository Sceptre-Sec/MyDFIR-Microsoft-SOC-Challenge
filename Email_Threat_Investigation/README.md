# Email Threat Investigation in Microsoft Defender

**Date:** 21 October 2025  
**Time zone:** London (UTC+01:00)  
**Analyst:** Sceptre

## Objective
Simulate a phishing campaign where one user’s email was delivered to their junk folder by Defender. Investigate the message, review its headers and authentication failures and use KQL to check whether other users were targeted.

   ## Key takeaway
I found that Microsoft’s built-in anti-spam policy kept quarantining my simulated phishing emails before my custom anti-phish rule could trigger, which was pretty frustrating during testing. After realising the default policies run first, I raised my custom anti-phish policy to **priority 1**, and that finally allowed my rule to evaluate before Microsoft’s filters and work as intended. The example message in this lab was ultimately delivered to the recipient’s junk folder rather than quarantined.

## Steps
1. **Created custom anti-phishing policy** – `DFIR-Sceptre-AntiPhish`  
   Configured impersonation protection, thresholds, and actions.  
2. **Validated in Explorer** – observed URL clicks to `neon-lab.test`.  
3. **Reviewed delivery details** – discovered failed SPF, DKIM, and DMARC; message delivered to junk.  
4. **Analysed message headers** – mismatched `From` and `Reply-To` domains with a custom header `X-Lab: marketplace-campaign-2025`.  
5. **Hunted with KQL** – confirmed multiple recipients received the same sender address and domain indicators.
6. **Documented findings in a formal investigation report** – summarised evidence and recommendations.



## Tools & skills
- Microsoft 365 Defender (Explorer, Threat Policies, Message Headers)  
- KQL (EmailEvents, EmailUrlInfo, UrlClickEvents)  
- Understanding of policy order and detection precedence
- Report writing and incident documentation  
- MITRE ATT&CK mapping – **T1566.002: Phishing: Spearphishing Link**

##




