
# Phishing Email Analysis — Task 2 (Cyber Security Internship)

**Prepared by:** Kabil (or your name)  
**Sample ID:** sample-001  
**Date:** 2025-10-22

---

## 1. Objective
Analyze a suspicious email sample and identify phishing indicators, techniques used, and recommended remediation and reporting steps. This report is based on a safe, contrived training sample (`phishing_sample.txt`) generated for educational purposes.

---

## 2. Sample Metadata (from phishing_sample.txt)
- **Source:** Generated for training by ChatGPT (safe example)  
- **Filename:** phishing_sample.txt  
- **How obtained:** Generated and saved locally for analysis  
- **Risk level (exercise):** High (multiple phishing indicators)  

---

## 3. Full Email (excerpt)
> From: "PayPal Support" <security@paypa1.com>  
> Subject: Urgent: Your PayPal account will be suspended  
> Date: Thu, 20 Oct 2025 09:10:00 +0000
>
> Email body (HTML): "Dear Customer, We detected unusual activity on your PayPal account... Click here to verify your account: Verify Now (http://paypal.security-update.example.fake/verify?uid=ABC123)"

(Full raw headers and body are stored in `phishing_sample.txt`.)

---

## 4. Detailed Analysis

### A. Sender address analysis
- **Displayed sender:** "PayPal Support" <security@paypa1.com>  
- **Observation:** Domain `paypa1.com` uses digit "1" in place of letter "l" — a classic typosquat designed to visually impersonate `paypal.com`.  
- **Risk indicator:** High — visually similar domain intended to trick recipients.

**Why it matters:** Attackers register lookalike domains to bypass cursory user inspection; many users trust the displayed name without checking the underlying email address.

---

### B. Header analysis (illustrative / training headers)
Headers included in the sample (illustrative):
```
Received: from unknown (HELO mail.example.fake) (192.0.2.45) by inbound.example.fake with SMTP
Authentication-Results: mx.example.fake; spf=fail (domain of paypa1.com does not designate 192.0.2.45 as permitted sender) smtp.mailfrom=security@paypa1.com
DKIM-Signature: (not present)
DMARC-Result: none
```

- **SPF:** FAIL (illustrative) — sending IP (192.0.2.45) is not authorized by the domain's SPF record. SPF failures indicate the email likely did not originate from the claimed domain's legitimate mail servers.
- **DKIM:** Missing — absence of a DKIM signature means the message lacks a cryptographic signature from the domain; legitimate providers typically sign outgoing mail.
- **DMARC:** None — no DMARC policy or no alignment; without DMARC enforcement, spoofed messages may not be rejected by receivers.

**Tools to verify headers in practice:**
- MXToolbox Email Header Analyzer: https://mxtoolbox.com/EmailHeaders.aspx  
- Google Admin Toolbox (Message Header Analyzer): https://toolbox.googleapps.com/apps/messageheader/  

**How to extract headers (examples):**
- Gmail: More → Show original → copy or Download Original (.eml)  
- Outlook: File → Properties → Internet headers (copy)  
- Thunderbird: Save as .eml (open in text editor for raw headers).

---

### C. Link analysis
- **Visible link text:** Verify Now  
- **Actual href:** http://paypal.security-update.example.fake/verify?uid=ABC123

**Problems found:**
- The domain `paypal.security-update.example.fake` attempts to appear related to PayPal by using `paypal` as a subdomain or prefix, but the real organizational domain should be `paypal.com`. Attackers use long or misleading hostnames to create perceived legitimacy.
- The URL uses HTTP (unsecured) and a non-paypal top-level/domain; real PayPal would use `https://www.paypal.com/...` or secure country-specific `paypal.*` with TLS and correct certificate.

**Recommended checks (safe):**  
- Hover to view the actual href (do not click).  
- Paste the URL into an online scanner (VirusTotal URL) only from a safe, isolated environment or via services that fetch and scan without exposing your machine.  
- Look for punycode or Unicode trickery (IDN homograph attacks) — check using `idn` tools or site inspectors.

---

### D. Attachment analysis
- **Attachment name (not opened):** invoice.docm (macro-enabled Word document)  
- **Why suspicious:** `.docm` can contain macros that execute code. Phishing campaigns commonly use macro-enabled documents to download malware or run scripts.  
- **Action:** Do NOT open attachments on your host machine. If analysis is required, open inside an isolated, offline VM with snapshots and monitoring tools, and ensure no network connectivity.

---

### E. Language, tone, and social engineering
- **Urgency/threat:** "Verify your identity within 24 hours or account will be permanently limited" — urgency increases the chance the recipient will act without checking details.  
- **Generic greeting:** "Dear Customer" instead of a personalized name — many legitimate services use full name or partial personalization.  
- **Tone:** Fear/Threat — common tactic to bypass rational verification.

**Social engineering techniques used:** urgency, impersonation of a trusted brand, plausible pretext (account limitation), and a call-to-action link.

---

### F. Visual & formatting cues
- Use of brand name in display name to increase perceived legitimacy.  
- Potential for slightly altered logo (in other samples) or low-resolution image. (Our sample is text-only but notes that real phishing emails sometimes include logos.)

---

## 5. Risk Assessment & Impact
- **Likelihood of success against untrained users:** High — typosquatting plus urgency is effective.  
- **Potential impact:** Credential compromise, account takeover, financial loss, malware infection from attachments.  
- **Overall risk rating (exercise):** High.

---

## 6. Recommended Immediate Actions (for recipients)
1. **Do not click links or open attachments.**  
2. **Do not reply** to the suspicious message.  
3. **Report** the email to your organization’s security team or email provider (Gmail: Report phishing).  
4. **Mark as spam/phishing** in the email client to help block similar future messages.  
5. **If you clicked or entered credentials,** immediately change your password on the legitimate site (using a browser bookmark, not the emailed link), enable MFA, and notify financial institutions if needed.  
6. **Collect evidence for investigation:** Save the raw email (.eml) and headers and forward to security team or provider as instructed (do not forward the suspicious email via "reply" which may expose content — use secure reporting channels).

---

## 7. Longer-term Defensive Recommendations (for organizations)
- Enforce SPF, DKIM, and DMARC with `p=quarantine` or `p=reject` policies.  
- Implement email filtering that flags DMARC/SPF/DKIM failures and suspicious sender domains.  
- Provide user awareness training focusing on typosquats, link hovering, and attachment handling.  
- Use URL rewriting/proxying for external links or sandbox attachments to analyze content safely.  
- Enforce multi-factor authentication (MFA) for critical accounts to limit impact of credential theft.

---

## 8. Tools & Resources Used / Suggested
- Header analyzers: MXToolbox, Google Admin Toolbox.  
- URL inspection: VirusTotal, URLScan.io (use safely).  
- Phishing sample resources & reading: Phishing.org, Kaspersky, Proofpoint, OWASP Phishing page.  
- For safe attachment analysis: isolated VM, Cuckoo Sandbox, or vendor-provided malware analysis platforms.

---

## 9. How I performed this analysis (reproducible steps)
1. Saved the contrived sample in `phishing_sample.txt`.  
2. Inspected the displayed sender and compared the domain to the legitimate brand domain.  
3. Reviewed illustrative headers for SPF/DKIM/DMARC status (for real emails: paste real headers into MXToolbox for automated analysis).  
4. Hovered and copied the link to inspect the real href (do not click).  
5. Noted the attachment name and flagged it as dangerous.  
6. Compiled findings into this report and assigned an overall risk rating.

---

## 10. Suggested Files for GitHub Submission (repo layout)
```
CyberSecurity_Task2_PhishingAnalysis/
├─ phishing_sample.txt            (provided)
├─ Phishing_Analysis_Report.md    (this file)
├─ screenshots/
│  ├─ screenshot_email_view.png   (optional visual mockup)
│  └─ screenshot_header_analysis.png
├─ README.md                      (short summary & submission instructions)
└─ LICENSE
```

### Suggested README.md content (short)
> This repository contains a contrived phishing sample and an analysis report demonstrating common phishing indicators: typosquatting, SPF/DKIM failures, mismatched links, urgent language, and a macro-enabled attachment. For educational purposes only.

---

## 11. Interview Question Answers (brief)
1. **What is phishing?**  
   Phishing is a social engineering attack where attackers send deceptive messages to trick recipients into revealing sensitive information or installing malware.  
2. **How to identify a phishing email?**  
   Check sender email address, hover-to-inspect links, validate headers (SPF/DKIM/DMARC), look for urgency, poor grammar, and unexpected attachments.  
3. **What is email spoofing?**  
   Email spoofing is forging email headers to make a message appear from a trusted source. SPF/DKIM/DMARC help prevent this.  
4. **Why are phishing emails dangerous?**  
   They can steal credentials, cause financial loss, or deliver malware.  
5. **How can you verify the sender’s authenticity?**  
   Verify SPF/DKIM/DMARC in headers, check sending IP, confirm via official channels (website or phone), and use known contact details rather than emailed links.  
6. **What tools can analyze email headers?**  
   MXToolbox, Google Admin Toolbox, and built-in mail server logs.  
7. **What actions should be taken on suspected phishing emails?**  
   Don’t click or open, report to security, mark as phishing, and isolate evidence.  
8. **How do attackers use social engineering in phishing?**  
   They exploit emotions (fear, curiosity, urgency) and trust in brands to trick users into hasty actions.

---

## 12. Conclusion
The analyzed sample contains multiple strong phishing indicators: a typosquatted sender domain, SPF failure and missing DKIM, mismatched links that do not belong to the legitimate brand domain, threatening/urgent language, and a macro-enabled attachment. All evidence indicates this is a phishing attempt and should be treated as malicious. The recommended immediate actions and organizational controls above will reduce the risk and limit impact.

---

## 13. Next steps I can prepare for you (pick one — I'll create it now)
- A `README.md` tailored for GitHub submission (with short instructions and copy-ready text).  
- PNG screenshots (mockups) showing the email and header analysis for the `screenshots/` folder.  
- A ZIP file containing all deliverables ready for upload.  

---
