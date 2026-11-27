# Phase 05 — Adversary Emulation

## Overview

This phase focuses on controlled adversary emulation within the SOC Homelab environment, replicating security-relevant adversarial behaviors and evaluating the effectiveness of the existing security monitoring infrastructure.

The primary objective is to develop practical experience in Security Operations, Threat Hunting, Incident Investigation, and Detection Engineering.

All activities are conducted within an authorized laboratory environment using dedicated virtual machines.

## Objectives

- Simulate adversarial behaviors mapped to the MITRE ATT&CK framework.
- Evaluate native Wazuh detection capabilities.
- Analyze Windows, Sysmon, and PowerShell telemetry.
- Perform alert triage and distinguish malicious activity from benign behavior.
- Reconstruct incident timelines using multiple telemetry sources.
- Identify collection, detection, and auditing gaps.
- Establish a baseline for custom detection engineering.
- Validate security monitoring improvements through controlled testing.

## Methodology

Each operation follows a structured security assessment methodology:

1. Define the operation scope and objectives.
2. Conduct controlled adversary emulation.
3. Collect endpoint and network telemetry.
4. Evaluate native SIEM alerts.
5. Perform alert triage and event correlation.
6. Reconstruct the incident timeline.
7. Identify detection and collection gaps.
8. Document findings and investigation limitations.
9. Develop and validate detection improvements.

Native detection capabilities are assessed before introducing custom rules, allowing for a meaningful comparison between baseline and improved detection coverage.

## Laboratory Environment

| System | IP Address | Role |
|---|---|---|
| SOC-SIEM | 10.10.10.20 | Wazuh SIEM |
| DC01 | 10.10.10.10 | Active Directory Domain Controller |
| CLIENT01 | 10.10.10.30 | Corporate Windows Endpoint |
| ATTACKER01 | 10.10.10.40 | Adversary Emulation Workstation |

**Active Directory Domain:** corp.soclab.test

## Operations

| Operation ID | Name | Methodology | Status |
|---|---|---|---|
| OP-001 | [Corporate Intrusion](01-Corporate-Intrusion/README.md) | Assumed Breach | In Progress |

### OP-001 — Corporate Intrusion

A multi-stage adversary emulation campaign designed to evaluate endpoint visibility, native detection capabilities, and incident investigation procedures within a simulated corporate Active Directory environment.

The operation begins with an assumed endpoint compromise and focuses on post-compromise behaviors.

The initial investigation covers:

- PowerShell execution.
- UDP command-and-control communication.
- Script file creation.
- Scheduled task persistence.
- Privileged execution context.
- Native detection assessment.
- Incident timeline reconstruction.
- Detection gap analysis.

## Independent Simulations

Additional controlled simulations will be conducted to evaluate individual MITRE ATT&CK techniques and validate detection coverage independently of the main operation.

## Security and Scope

All activities are restricted to authorized laboratory systems.

The project emphasizes defensive analysis, telemetry validation, and detection engineering rather than the development or distribution of operational offensive tooling.

Initial access assumptions, pre-existing administrative privileges, and investigation limitations are explicitly documented to ensure accurate interpretation of the results.

## Expected Outcomes

- Documented adversary emulation investigations.
- Evidence-based alert classification.
- Correlated incident timelines.
- Detection coverage matrices.
- Detection gap register.
- Improved security telemetry.
- Validated custom detection rules.
- Practical incident response experience.

## Phase Status

- [x] Adversary emulation methodology defined
- [x] Laboratory environment prepared
- [x] OP-001 initiated
- [x] Initial native detection assessment
- [x] Initial endpoint telemetry validation
- [ ] OP-001 documentation completed
- [ ] Telemetry improvements validated
- [ ] Custom detection engineering
- [ ] Detection validation
- [ ] Independent simulations completed
- [ ] Phase completed
