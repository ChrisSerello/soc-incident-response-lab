# Incident Report — IR-2026-001

**Classification:** Internal — Confidential  
**Severity:** Critical  
**Status:** Closed  
**Reported by:** SOC Analyst  
**Date Created:** 2026-03-20  
**Date Closed:** 2026-03-21  

---

## 1. Executive Summary

On 2026-03-20, a brute force attack was detected against the internal Windows host **WINHOST01**. The threat actor, originating from IP **192.168.1.105**, conducted 47 failed authentication attempts over approximately 17 minutes before successfully authenticating. Following access, the attacker executed PowerShell commands to download a remote payload and ran encoded commands to evade detection. Internal reconnaissance activity was subsequently observed. The incident was contained within 2 hours of detection.

---

## 2. Incident Classification

| Field | Value |
|---|---|
| Incident ID | IR-2026-001 |
| Category | Unauthorized Access / Credential Attack |
| Severity | Critical |
| Affected Host | WINHOST01 (192.168.1.50) |
| Attacker IP | 192.168.1.105 |
| Attack Vector | Network — RDP / SMB |
| MITRE ATT&CK Tactics | Initial Access, Execution, Discovery |

---

## 3. Timeline of Events

| Timestamp (UTC) | Event Description | Source Log | Event ID |
|---|---|---|---|
| 2026-03-20 02:14:03 | First failed logon attempt from 192.168.1.105 | Security | 4625 |
| 2026-03-20 02:14:03 – 02:31:47 | 47 consecutive failed logon attempts | Security | 4625 |
| 2026-03-20 02:31:52 | Successful logon — user `administrator` | Security | 4624 |
| 2026-03-20 02:33:11 | PowerShell process launched with download cradle | Sysmon / PS-Operational | 4688 / 4104 |
| 2026-03-20 02:34:05 | PowerShell executed with `-EncodedCommand` flag | Sysmon / PS-Operational | 4688 / 4104 |
| 2026-03-20 02:37:22 | `net user`, `ipconfig /all`, `whoami` executed | Sysmon | 4688 |
| 2026-03-20 04:05:00 | Host isolated — network access revoked | N/A | N/A |
| 2026-03-20 04:30:00 | Password reset for `administrator` account | N/A | N/A |
| 2026-03-21 09:00:00 | Forensic review complete — incident closed | N/A | N/A |

---

## 4. Indicators of Compromise (IOCs)

### Network
| Type | Value | Context |
|---|---|---|
| IP Address | 192.168.1.105 | Attack source — brute force origin |
| Destination Port | 3389 (RDP) | Primary attack vector |

### Host
| Type | Value | Context |
|---|---|---|
| Username | administrator | Compromised account |
| Process | powershell.exe | Used for download and execution |
| Command | `-EncodedCommand` flag | Obfuscated execution |
| Command | `net user`, `ipconfig /all`, `whoami` | Reconnaissance commands |

### Files (suspected — not confirmed recovered)
| Type | Value | Context |
|---|---|---|
| File | Unknown (downloaded via PS) | Payload from remote URL |

---

## 5. Detection Details

| Detection ID | Description | First Triggered | SPL File |
|---|---|---|---|
| DET-001 | Brute force — 10+ failures in 5 min | 2026-03-20 02:19 | `DET-001_brute_force_logon.spl` |
| DET-002 | Brute force followed by success | 2026-03-20 02:32 | `DET-002_brute_force_success.spl` |
| DET-003 | PowerShell download cradle | 2026-03-20 02:33 | `DET-003_ps_download.spl` |
| DET-004 | Encoded PowerShell command | 2026-03-20 02:34 | `DET-004_ps_encoded.spl` |
| DET-005 | Internal recon commands | 2026-03-20 02:37 | `DET-005_recon.spl` |

---

## 6. Impact Assessment

| Category | Impact |
|---|---|
| Confidentiality | Possible — attacker had admin access for ~1.5 hours |
| Integrity | Unknown — payload content not recovered |
| Availability | Low — host isolated but services briefly disrupted |
| Data Exfiltration | Unconfirmed — no egress traffic anomalies detected |

---

## 7. Containment Actions

1. **Network isolation** of WINHOST01 (firewall rule blocking all traffic)
2. **Account lockout** of the `administrator` account
3. **Snapshot** of the compromised host disk for forensic analysis
4. **RDP access** restricted to VPN-only connections

---

## 8. Eradication & Recovery

1. Full **password reset** for all local administrator accounts on affected and neighboring hosts
2. **MFA** enforced on all RDP-accessible accounts
3. Host **reimaged** from clean baseline after forensic snapshot
4. **Account lockout policy** configured: lock after 5 failed attempts
5. Splunk alerts **tuned and enabled** for all 5 detection rules

---

## 9. Root Cause Analysis

- **Primary cause:** No account lockout policy was configured on WINHOST01, allowing unlimited authentication attempts.
- **Contributing factor:** The `administrator` account used a weak password that was successfully guessed after 47 attempts.
- **Detection gap:** No real-time alerting was active for failed logon thresholds prior to this incident.

---

## 10. Lessons Learned

| Finding | Recommendation | Priority |
|---|---|---|
| No account lockout policy | Configure lockout after 5 failures | Critical |
| Weak administrator password | Enforce strong password policy + MFA | Critical |
| RDP exposed on LAN without MFA | Restrict RDP to VPN + require MFA | High |
| No real-time SIEM alerting | Enable all 5 detection rules in Splunk | High |
| No EDR on WINHOST01 | Deploy endpoint detection tool | Medium |

---

## 11. MITRE ATT&CK Mapping

| Tactic | Technique | ID | Evidence |
|---|---|---|---|
| Initial Access | Valid Accounts (Brute Force) | T1110.001 | 47 Event ID 4625 + 1 Event ID 4624 |
| Execution | PowerShell | T1059.001 | powershell.exe with Invoke-WebRequest |
| Defense Evasion | Obfuscated Files or Information | T1027 | `-EncodedCommand` parameter |
| Discovery | System Information Discovery | T1082 | `ipconfig /all`, `whoami` |
| Discovery | Account Discovery | T1087 | `net user` |

---

*Report authored by SOC Blue Team | Lab exercise — simulated environment*
