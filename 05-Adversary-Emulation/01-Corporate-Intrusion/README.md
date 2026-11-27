# OP-001 — Corporate Intrusion

## Operation Overview

OP-001 is a controlled adversary emulation campaign designed to simulate a corporate endpoint compromise within an Active Directory environment.

The operation follows an Assumed Breach methodology, focusing on post-compromise activity, security telemetry, native SIEM detection capabilities, and incident investigation.

Rather than focusing exclusively on offensive execution, this operation evaluates the ability of a Security Operations Center to identify, investigate, correlate, and respond to adversarial behavior.

## Operation Profile

| Attribute | Description |
|---|---|
| Operation ID | OP-001 |
| Operation Name | Corporate Intrusion |
| Start Date | November 27, 2026 |
| Methodology | Assumed Breach |
| Target Endpoint | CLIENT01 |
| Initial User | CORP\james.miller |
| Administrative Test Account | CORP\admin.john |
| Adversary System | ATTACKER01 |
| SIEM | Wazuh |
| Status | In Progress |

## Scenario

A corporate Windows endpoint is assumed to have been compromised.

A custom PowerShell-based command-and-control prototype was developed for controlled laboratory experimentation.

The initial investigation focuses on PowerShell execution, UDP network communication, local script file creation, and scheduled task persistence.

Administrative privileges were explicitly provided during privileged testing. No privilege escalation exploit has been demonstrated at this stage.

Initial access is treated as a scenario assumption rather than an experimentally validated attack technique.

## Operation Objectives

- Evaluate native Wazuh detection capabilities.
- Investigate suspicious PowerShell activity.
- Analyze endpoint network telemetry.
- Identify persistence-related artifacts.
- Correlate events across multiple telemetry sources.
- Distinguish simulated malicious activity from legitimate administrative behavior.
- Investigate detection and collection gaps.
- Establish a baseline for detection engineering.
- Validate subsequent monitoring improvements.

## MITRE ATT&CK Coverage

| Technique ID | Technique | Status |
|---|---|---|
| T1059.001 | PowerShell | Observed |
| T1095 | Non-Application Layer Protocol | Observed |
| T1053.005 | Scheduled Task | Observed |

Additional techniques will be documented as the operation progresses.

MITRE ATT&CK classifications assigned by native Wazuh rules are evaluated independently and are not automatically considered evidence that the corresponding technique occurred.

## Initial Investigation Findings

The initial investigation identified several important findings:

- PowerShell Script Block Logging successfully captured the emulation script.
- Native Wazuh rules generated alerts associated with PowerShell execution and script file creation.
- Sysmon recorded UDP network communication locally.
- Windows Task Scheduler logs confirmed task registration and execution.
- No corresponding UDP communication or Task Scheduler alerts were identified in the investigated Wazuh time windows.
- Scheduled task security auditing was not enabled.
- Raw event archiving was disabled on the Wazuh Manager.
- Several native alerts presented potentially inaccurate MITRE ATT&CK classifications.
- High-severity alerts were associated with apparently legitimate PowerShell policy-check activity.
- Timestamp inconsistencies were identified in Sysmon network events.

These findings establish the initial detection baseline for subsequent telemetry improvements and detection engineering.

## Investigation Documentation

| Document | Description |
|---|---|
| [01 — Operation Overview](01-Operation-Overview.md) | Scenario, scope, assumptions, and objectives |
| [02 — C2 Architecture](02-C2-Architecture.md) | Communication architecture and defensive considerations |
| [03 — Execution and Persistence](03-Execution-and-Persistence.md) | Observed behaviors and endpoint evidence |
| [04 — Native Detection Assessment](04-Native-Detection-Assessment.md) | Native alerts, triage, and classification |
| [05 — Incident Timeline](05-Incident-Timeline.md) | Chronological reconstruction of observed activity |
| [06 — Detection Gap Analysis](06-Detection-Gap-Analysis.md) | Identified monitoring gaps and investigation limitations |

## Operation Progress

### Initial Investigation

- [x] Assumed Breach scenario defined
- [x] Custom C2 prototype developed
- [x] PowerShell execution observed
- [x] UDP communication observed
- [x] Scheduled task persistence observed
- [x] Native alert assessment
- [x] Alert triage
- [x] Endpoint telemetry validation
- [x] Preliminary incident timeline
- [x] Initial detection gap identification
- [ ] Investigation documentation completed

### Detection Improvements

- [ ] Windows auditing improvements
- [ ] Event collection validation
- [ ] Timestamp inconsistency investigation
- [ ] Custom detection development
- [ ] Detection validation
- [ ] False-positive assessment
- [ ] Before-and-after detection coverage comparison

### Additional Adversary Emulation

- [ ] Discovery
- [ ] Privilege escalation assessment
- [ ] Credential access assessment
- [ ] Lateral movement
- [ ] Collection
- [ ] Exfiltration
- [ ] Impact

## Evidence Management

Investigation evidence is presented through:

- Wazuh dashboard screenshots.
- Relevant Windows event excerpts.
- Sysmon telemetry.
- Selected JSON alert fields.
- Incident timelines.
- Detection coverage matrices.

Evidence is selected according to its relevance to the investigation.

Operational offensive source code is not included in this documentation.

## Investigation Limitations

- Initial access was assumed rather than executed.
- Administrative privileges were provided during privileged testing.
- The SIEM was unavailable during part of the initial activity.
- Raw event archives were disabled during the initial assessment.
- Missing alerts do not necessarily indicate missing telemetry.
- Network event timestamps require further validation.
- Some alert classifications remain preliminary.

These limitations are documented to ensure that conclusions remain evidence-based and reproducible.

## Disclaimer

All activities were conducted within an authorized SOC Homelab environment using dedicated virtual machines.

This project is intended for cybersecurity education, defensive security research, incident investigation, and detection engineering.

---

[Return to Phase 05 — Adversary Emulation](../README.md)
