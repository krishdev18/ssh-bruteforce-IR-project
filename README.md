# 🔐 SSH Brute Force Attack — Detection & Incident Response Lab

![Status](https://img.shields.io/badge/Status-Resolved-brightgreen)
![Severity](https://img.shields.io/badge/Severity-HIGH-red)
![SIEM](https://img.shields.io/badge/SIEM-Splunk-orange)
![Platform](https://img.shields.io/badge/Platform-Windows%2010-blue)

---

## 📋 Project Overview

This project documents a complete **end-to-end Incident Response** for an SSH Brute Force attack conducted in a home lab environment. It covers attack simulation, detection using Splunk SIEM, SOC L1 investigation, MITRE ATT&CK mapping, and full remediation.

| Field | Details |
|---|---|
| **Incident ID** | IR-2026-001 |
| **Date** | March 6, 2026 |
| **Attack Type** | SSH Brute Force / Credential Attack |
| **Severity** | HIGH |
| **Attacker** | krish (Kali Linux) |
| **Target** | testuser account (Windows VM) |
| **Outcome** | Account Compromised → Remediated |

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| Kali Linux | Attacker machine |
| Hydra | SSH brute force tool |
| Windows 10 VM | Target system |
| Sysmon | Endpoint telemetry |
| Splunk 10.2.1 | SIEM — detection & alerting |
| Windows Firewall | Containment & hardening |

---

## ⚔️ Attack Summary

An automated SSH brute force attack was launched from Kali Linux against the `testuser` account on the Windows target. Using Hydra, the attacker made **7 failed login attempts** followed by **10 successful unauthorized logins** — all within milliseconds, confirming automated tooling.

**Key findings:**
- All attempts occurred in under 1 second
- Weak password allowed compromise after only 7 attempts
- Windows Security Event Logs captured full attack (EventCode 4625 & 4624)
- Sysmon logged 76 sshd.exe process events during attack window

---

## 🔍 Detection Methodology

Detection was performed using **Splunk SIEM** querying Windows Security and Sysmon logs.

**Failed Login Detection (EventCode 4625):**
```spl
index=main EventCode=4625
| stats count by host, Account_Name
| sort -count
```

**Successful Login Detection (EventCode 4624):**
```spl
index=main EventCode=4624 Account_Name=testuser
| table _time, host, Account_Name, LogonType
| sort _time
```

**Sysmon Process Evidence:**
```spl
index=main source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
| stats count by Image, EventCode
| sort -count
```

---

## 🗺️ MITRE ATT&CK Mapping

| Tactic | Technique | ID | Evidence |
|---|---|---|---|
| Credential Access | Brute Force | T1110 | 7x EventCode 4625 failed logins |
| Credential Access | Password Guessing | T1110.001 | Automated rapid attempts via Hydra |
| Initial Access | Valid Accounts | T1078 | 10x EventCode 4624 successful logins |
| Lateral Movement | Remote Services: SSH | T1021.004 | sshd.exe — 76 Sysmon events |

---

## 🛡️ Response Actions

| Step | Action | Status |
|---|---|---|
| 1 | Block attacker IP in Windows Firewall | ✅ Done |
| 2 | Disable compromised testuser account | ✅ Done |
| 3 | Reset testuser password | ✅ Done |
| 4 | Re-enable account with strong credentials | ✅ Done |
| 5 | Create Splunk Brute Force Detection alert | ✅ Done |

---

## 📸 Screenshots

| File | Description |
|---|---|
| ph3-step1-failed-logins.png | 7x EventCode 4625 failed login events in Splunk |
| ph3-step2-attacker-ip.png | Attacker host identified in Splunk |
| ph3-step3-attack-timeline.png | Timechart showing attack spike |
| ph3-step4-successful-login.png | 10x EventCode 4624 successful logins confirmed |
| ph3-step5-alert-rule.png | Splunk Brute Force Detection alert rule |
| ph4-step1-investigation.png | SOC L1 investigation — 6 questions answered |
| ph4-step2-sysmon-logs.png | Sysmon logs showing sshd.exe activity |
| ph4-step3-mitre-mapping.png | MITRE ATT&CK T1110 technique page |
| ph5-step1-firewall-block.png | Windows Firewall rule blocking attacker |
| ph5-step2-account-disabled.png | testuser account disabled confirmation |
| ph5-step3-password-reset.png | Password reset completion confirmation |

---

## 📄 Incident Report

Full professional IR report available: [`IR-2026-001-Brute-Force-Report.docx`](./IR-2026-001-Brute-Force-Report.docx)

---

## 🎓 Key Learnings

- Splunk successfully detected brute force via EventCode 4625/4624 correlation
- Sysmon provided critical process-level evidence (sshd.exe activity)
- Weak passwords allow compromise in very few attempts — password policy is critical
- Automated attacks happen in milliseconds — real-time alerting is essential
- Account lockout policy would have stopped this attack after 3-5 attempts

---

## 👤 Author

**Hari Krishnan** | Home Lab | SOC L1 Analyst  
Incident Date: March 6, 2026
