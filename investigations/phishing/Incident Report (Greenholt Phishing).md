# SOC Incident Report: The Greenholt Phish Triage

## 1. Executive Summary

| Metadata | Details |
| :--- | :--- |
| **Incident / Case Title** | Suspicious Inbound Email – "The Greenholt Phish" |
| **Alert Date / Time** | June 10, 2020 / 10:15:20 UTC |
| **Severity Level** | High |
| **Incident Classification** | **True Positive** (Spearphishing / Spoofing & Malicious Attachment) |
| **Analyst Name** | Shreyansh Lohumi |

### Summary Statement
A suspicious email with subject `Transfer Reference Number(09674321)` was delivered to an internal employee. The email claimed to contain a transfer document from **Mr. James Jackson** (`info@mutawamarine.com`). Header analysis confirmed spoofing and authentication failures, as the email originated from a Hostwinds LLC server (`192.119.71.157`) and directed replies to a external Gmail-style mailbox (`info.mutawamarine@mail.com`). The email contained a **400.26 KB** archive attachment named `SWT_#09674321____PDF__.CAB`, which double-extension analysis revealed to actually be a **RAR compressed archive** (`SHA256: 2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f`).

---

## 2. Email Header & Authentication Analysis

| Field | Analyzed Value | Assessment / Status |
| :--- | :--- | :--- |
| **Sender (From)** | `Mr. James Jackson <info@mutawamarine.com>` | Spoofed / Display Name Masquerading |
| **Reply-To** | `info.mutawamarine@mail.com` | **Mismatch** (External lure mailbox) |
| **Subject Line** | `SWT_#09674321____PDF__` | Transfer Ref: `09674321` (Social Engineering) |
| **Originating Public IP** | `192.119.71.157` | Untrusted / Malicious Relay |
| **IP Infrastructure** | Hostwinds LLC | VPS/Hosting Provider |
| **SPF Result** | **FAIL** | IP `192.119.71.157` not in SPF list |
| **DKIM Result** | **FAIL / NONE** | Missing / Invalid cryptographic signature |
| **DMARC Result** | **FAIL** | SPF/DKIM alignment check failed |

### DNS Policy Verification (`mutawamarine.com`)
* **SPF Record (`TXT`):** `v=spf1 include:spf.protection.outlook.com -all`  
  *(Only Microsoft 365 servers are authorized. Originating IP `192.119.71.157` violates strict `-all` policy).*
* **DMARC Record (`TXT`):** `v=DMARC1; p=quarantine; fo=1`  
  *(Policy demands quarantine on alignment failure).*

---

## 3. Investigation Timeline & Artifact Analysis

### Chronological Timeline
* `2020-06-10 10:15:20 UTC` — Email received from `192.119.71.157` and delivered to inbox.
* `2020-06-10 10:42:00 UTC` — User flagged message to SOC due to suspicious `Reply-To` and attachment naming.
* `2020-06-10 10:45:12 UTC` — SOC Analyst initiated triage, extracted `.eml` headers, and isolated attachment.

### Payload & Content Inspection
* **Body / Lure Strategy:** Urgency-driven financial lure referencing transfer number `09674321`.
* **Attachment Analysis:**
  * **Displayed Filename:** `SWT_#09674321____PDF__.CAB`
  * **File Size:** `400.26 KB`
  * **MIME/Header Extension:** `.CAB`
  * **Actual Magic Byte Extension:** **`.RAR` archive** *(Double-extension/masquerading technique to trick email filters and users)*.
  * **SHA256 Hash:** `2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f`

---

## 4. MITRE ATT&CK Mapping

| Tactic | Technique ID | Technique Name | Observed Evidence |
| :--- | :--- | :--- | :--- |
| **Initial Access** | `T1566.001` | Spearphishing Attachment | Delivery of `SWT_#09674321____PDF__.CAB` |
| **Defense Evasion** | `T1036.007` | Double File Extension | File named `.PDF__.CAB` to trick user into opening |
| **Defense Evasion** | `T1036.008` | Masquerading | `From:` address domain spoofed (`mutawamarine.com`) |
| **Resource Development**| `T1586.002` | Compromised Accounts | Use of external `mail.com` infrastructure for `Reply-To` |

---

## 5. Indicators of Compromise (IOCs)

* **Spoofed Sender:** `info@mutawamarine.com`
* **Attacker Reply Mailbox:** `info.mutawamarine@mail.com`
* **Originating IP:** `192.119.71.157` (Hostwinds LLC)
* **Malicious File Attachment:** `SWT_#09674321____PDF__.CAB`
* **Attachment SHA256:** `2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f`

---

## 6. Containment, Eradication & Remediation Actions

- [x] **Email Purge:** Deleted email from recipient inbox and searched mail gateway logs for identical subject string `SWT_#09674321`.
- [x] **Perimeter Block:** Added originating IP `192.119.71.157` and domain `info.mutawamarine@mail.com` to firewall and secure mail gateway blocklists.
- [x] **EDR Hash Block:** Added `2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f` to global EDR execution blocklist.
- [x] **Host Verification:** Checked target workstation logs to confirm the archive was not extracted or executed.
