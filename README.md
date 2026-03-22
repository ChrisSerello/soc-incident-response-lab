# SOC Incident Response Lab

![Splunk](https://img.shields.io/badge/Splunk-SIEM-blue) ![Status](https://img.shields.io/badge/Status-Active-green) ![License](https://img.shields.io/badge/License-MIT-yellow)

## Overview

This repository documents a hands-on SOC (Security Operations Center) lab exercise simulating a real-world attack scenario and the full incident response lifecycle. The lab environment uses **Splunk** as the primary SIEM platform.

The simulated incident covers:
- Brute force login attempts against a Windows host
- Successful unauthorized access
- PowerShell-based payload download
- Encoded PowerShell execution (obfuscation technique)
- Internal reconnaissance activity

## Repository Structure

```
soc-incident-response-lab/
├── README.md                        # This file
├── INCIDENT_REPORT.md               # Full incident report (IR-2026-001)
├── detections/                      # Splunk SPL detection queries
│   ├── DET-001_brute_force_logon.spl    # Multiple failed logon attempts
│   ├── DET-002_brute_force_success.spl  # Failed logons followed by success
│   ├── DET-003_ps_download.spl          # PowerShell download cradle activity
│   ├── DET-004_ps_encoded.spl           # Encoded PowerShell command detection
│   └── DET-005_recon.spl                # Internal reconnaissance (net/ipconfig)
├── dashboard/
│   └── soc_incident_dashboard.xml   # Splunk dashboard XML (importable)
├── configs/
│   └── inputs.conf                  # Splunk universal forwarder config
└── screenshots/
    ├── dashboard.png                # SOC dashboard view
    ├── det001.png                   # Detection DET-001 results
    └── timeline.png                 # Attack timeline visualization
```

## Attack Timeline

| Time (UTC) | Event | Detection |
|---|---|---|
| 2026-03-20 02:14 | Brute force begins (192.168.1.105 → WINHOST01) | DET-001 |
| 2026-03-20 02:31 | Successful logon after 47 failures | DET-002 |
| 2026-03-20 02:33 | PowerShell download cradle executed | DET-003 |
| 2026-03-20 02:34 | Encoded PowerShell command run | DET-004 |
| 2026-03-20 02:37 | Internal recon commands observed | DET-005 |

## Detection Coverage

| ID | Detection Name | Technique (MITRE ATT&CK) | Severity |
|---|---|---|---|
| DET-001 | Brute Force Logon Attempts | T1110.001 - Password Guessing | High |
| DET-002 | Brute Force Followed by Success | T1110.001 - Password Guessing | Critical |
| DET-003 | PowerShell Download Cradle | T1059.001 - PowerShell | High |
| DET-004 | Encoded PowerShell Execution | T1027 - Obfuscated Files | High |
| DET-005 | Internal Reconnaissance | T1082 - System Info Discovery | Medium |

## Lab Environment

- **SIEM:** Splunk Enterprise (local instance)
- **Log Sources:** Windows Event Logs (Security, System, PowerShell/Operational)
- **Simulated Host:** WINHOST01 (Windows 10)
- **Attack Source IP:** 192.168.1.105
- **Event IDs monitored:** 4625, 4624, 4688, 4104

## How to Use

### Import the Dashboard
1. Open Splunk Web → Dashboards → Create New Dashboard
2. Select **Classic Dashboard**
3. Click **Source** and paste the contents of `dashboard/soc_incident_dashboard.xml`
4. Save and view

### Run Detection Queries
1. Open Splunk → Search & Reporting
2. Copy and paste any `.spl` file from `detections/`
3. Adjust the time range as needed

### Configure the Forwarder
1. Place `configs/inputs.conf` in `$SPLUNK_HOME/etc/system/local/`
2. Restart the Splunk Universal Forwarder

## Incident Report

See [INCIDENT_REPORT.md](./INCIDENT_REPORT.md) for the full structured incident report including:
- Executive Summary
- Indicators of Compromise (IOCs)
- Timeline of Events
- Containment & Remediation Actions
- Lessons Learned

## References

- [MITRE ATT&CK Framework](https://attack.mitre.org/)
- [Splunk Security Essentials](https://splunkbase.splunk.com/app/3435)
- [NIST SP 800-61 — Computer Security Incident Handling Guide](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)
- [Windows Security Event IDs](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/)

---

*Lab created as part of SOC/Blue Team certification study. All activity is simulated in an isolated environment.*
