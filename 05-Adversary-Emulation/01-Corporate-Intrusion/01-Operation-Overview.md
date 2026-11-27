# OP-001 — Operation Overview

## 1. Introduction

OP-001 — Corporate Intrusion is a controlled adversary emulation operation conducted within the SOC Homelab environment.

The operation is designed to evaluate the effectiveness of endpoint security monitoring, native SIEM detections, threat hunting procedures, and incident investigation capabilities within a simulated corporate Active Directory environment.

The primary focus is defensive security analysis, with adversarial behaviors serving as the source of security telemetry.

## 2. Threat Scenario

The scenario assumes that an adversary has gained initial access to a corporate Windows workstation associated with a standard domain user.

The simulated compromised endpoint is CLIENT01, initially associated with the following identity:

- Username: james.miller
- Domain: CORP
- Department: Sales
- Account Type: Standard Domain User

The operation investigates post-compromise behaviors, including PowerShell execution, command-and-control communication, file creation, and persistence.

Additional adversarial behaviors will be assessed as the operation progresses.

## 3. Assumed Breach Methodology

An Assumed Breach methodology was selected to prioritize post-compromise visibility and defensive security capabilities.

Initial access is treated as an established scenario condition rather than an experimentally validated attack technique.

No phishing campaign, malicious document delivery, or vulnerability exploitation was performed during the initial stage.

This approach allows the investigation to focus on:

- Endpoint telemetry.
- Security monitoring coverage.
- Adversarial behavior identification.
- Alert triage.
- Event correlation.
- Persistence detection.
- Incident reconstruction.
- Detection engineering.

## 4. Operation Scope

### 4.1 — Laboratory Assets

| Asset | IP Address | Operating System | Role |
|---|---|---|---|
| CLIENT01 | 10.10.10.30 | Windows 11 Pro | Target endpoint |
| ATTACKER01 | 10.10.10.40 | Kali Linux | Adversary emulation system |
| DC01 | 10.10.10.10 | Windows Server 2022 | Domain controller |
| SOC-SIEM | 10.10.10.20 | Ubuntu Server | Security monitoring |

All systems are hosted within the dedicated Hyper-V SOC Homelab network.

### 4.2 — Active Directory Environment

| Attribute | Value |
|---|---|
| Domain | corp.soclab.test |
| NetBIOS Name | CORP |
| Initial User | CORP\james.miller |
| Administrative Test Account | CORP\admin.john |
| Target Endpoint | CLIENT01 |

### 4.3 — Security Monitoring Components

The following security monitoring components were deployed before the operation:

- Wazuh Manager.
- Wazuh Indexer.
- Wazuh Dashboard.
- Wazuh Windows Agents.
- Microsoft Sysmon.
- Windows Advanced Audit Policies.
- PowerShell Script Block Logging.
- PowerShell Module Logging.
- Windows Task Scheduler Operational Logging.
- Microsoft Defender Operational Logging.
- WMI Activity Logging.

The existing monitoring configuration represents the baseline against which detection coverage is evaluated.

## 5. Rules of Engagement

The following restrictions apply throughout the operation:

- Activities are restricted to authorized laboratory assets.
- No external systems are targeted.
- No real organizational or personal data is used.
- Security controls are not intentionally disabled to facilitate execution.
- Administrative privileges provided for testing are explicitly documented.
- Observed behaviors are distinguished from assumed or planned techniques.
- Operational offensive source code is excluded from the published documentation.
- Detection validation is conducted using controlled, non-destructive simulations.

The laboratory uses a dedicated internal Hyper-V network with NAT connectivity. It is not a fully isolated or air-gapped environment.

## 6. Operation Stages

| Stage | Objective | Status |
|---|---|---|
| Initial Access | Establish the assumed compromise scenario | Assumed |
| Execution | Evaluate PowerShell execution visibility | Observed |
| Command and Control | Evaluate network communication visibility | Observed |
| Persistence | Investigate scheduled task artifacts | Observed |
| Discovery | Evaluate system and domain discovery visibility | Planned |
| Privilege Escalation | Assess privilege boundaries and detection coverage | Planned |
| Credential Access | Evaluate credential-related security telemetry | Planned |
| Lateral Movement | Evaluate inter-host activity visibility | Planned |
| Collection | Assess collection-related detection coverage | Planned |
| Exfiltration | Evaluate controlled synthetic data transfer visibility | Planned |
| Impact | Evaluate non-destructive impact simulations | Planned |

## 7. MITRE ATT&CK Mapping

The following techniques were observed during the initial operation stage:

| Technique ID | Technique | Tactic |
|---|---|---|
| T1059.001 | PowerShell | Execution |
| T1095 | Non-Application Layer Protocol | Command and Control |
| T1053.005 | Scheduled Task | Persistence |

Technique mappings are based on observed behavior and available evidence.

MITRE ATT&CK mappings automatically assigned by native SIEM rules are independently reviewed during alert triage.

An alert associated with a particular technique does not necessarily establish that the technique was successfully performed.

## 8. Investigation Methodology

The operation follows a structured SOC investigation workflow:

1. Establish the existing security monitoring baseline.
2. Observe controlled adversarial activity.
3. Identify native Wazuh alerts.
4. Perform individual alert triage.
5. Validate endpoint telemetry.
6. Correlate events across different log sources.
7. Reconstruct the incident timeline.
8. Identify auditing, collection, and detection gaps.
9. Document findings and limitations.
10. Develop and validate detection improvements.

Native detection capabilities are evaluated before custom detection rules are introduced.

This approach preserves the initial detection baseline and supports a meaningful before-and-after comparison.

## 9. Investigation Success Criteria

The initial investigation is considered successful when the following objectives are achieved:

- Relevant endpoint artifacts are identified.
- Observed activity is correlated across multiple telemetry sources.
- Native SIEM alerts are evaluated and classified.
- Legitimate administrative activity is distinguished from simulated malicious behavior.
- Missing alerts are investigated without assuming missing telemetry.
- Detection and auditing limitations are documented.
- Evidence-based recommendations are established.

Successful adversary emulation does not require every simulated behavior to generate an alert.

Undetected activity represents an opportunity to evaluate and improve security monitoring coverage.

## 10. Assumptions and Limitations

### Initial Access

Initial access was assumed. No specific initial access technique was experimentally validated.

### Administrative Privileges

Administrative credentials were explicitly provided during privileged testing.

Execution under an administrative account or the SYSTEM security context does not independently demonstrate successful privilege escalation.

### SIEM Availability

The Wazuh server was unavailable during part of the initial activity.

Consequently, complete centralized visibility of the original activity cannot be guaranteed.

### Raw Event Retention

Raw event archiving was disabled during the initial assessment.

This limits retrospective verification of events that may have reached the Wazuh Manager without generating indexed alerts.

### Timestamp Reliability

Timestamp inconsistencies were identified in Sysmon network telemetry.

The incident timeline remains provisional until the discrepancy is investigated.

## 11. Expected Deliverables

- Operation architecture documentation.
- Endpoint telemetry analysis.
- Native detection assessment.
- Alert triage findings.
- Incident timeline.
- MITRE ATT&CK coverage assessment.
- Detection gap register.
- Telemetry improvement recommendations.
- Custom detection rules.
- Detection validation results.
- Before-and-after coverage comparison.

## 12. Conclusion

OP-001 establishes a practical framework for evaluating security monitoring capabilities within a simulated corporate environment.

The operation prioritizes evidence-based investigation, accurate behavioral classification, and continuous detection improvement.

Its purpose is not merely to reproduce adversarial activity, but to understand how that activity can be identified, investigated, and addressed by a Security Operations Center.

---

[Return to OP-001 — Corporate Intrusion](README.md)
