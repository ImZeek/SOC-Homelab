# Phase 03 — Security Monitoring & Telemetry Collection

## Overview

This phase focuses on establishing centralized security monitoring and endpoint telemetry collection across the corporate Active Directory environment.

Wazuh agents, Sysmon, Windows Advanced Audit Policies, and PowerShell Logging were deployed and configured to provide visibility into endpoint activity, authentication events, process execution, and potential security threats.

The resulting monitoring infrastructure establishes the foundation for future adversary simulations, detection engineering, threat hunting, and incident response activities.

## Monitored Assets

| Hostname | Operating System | IP Address | Role |
|---|---|---|---|
| DC01 | Windows Server 2022 | 10.10.10.10 | Domain Controller |
| CLIENT01 | Windows 11 Pro | 10.10.10.30 | Corporate Workstation |
| SOC-SIEM | Ubuntu Server 24.04 LTS | 10.10.10.20 | Centralized Security Monitoring |

## Implemented Capabilities

- Wazuh agent deployment and centralized management.
- Role-based agent grouping.
- Sysmon deployment using the Sysmon Modular configuration.
- Windows Advanced Audit Policies through Group Policy.
- PowerShell Module Logging.
- PowerShell Script Block Logging.
- Process Creation Command Line Auditing.
- Centralized Windows Event Channel collection.
- Endpoint-to-SIEM telemetry validation.

## Monitored Event Channels

| Event Channel | Monitoring Purpose |
|---|---|
| Security | Authentication, account management and privilege usage |
| System | System activity and service-related events |
| Application | Application-level events |
| Sysmon Operational | Advanced endpoint activity |
| PowerShell Operational | PowerShell commands and script execution |
| Task Scheduler Operational | Scheduled task activity |
| WMI Activity Operational | Windows Management Instrumentation activity |
| Windows Defender Operational | Antivirus detections and security-related activity |

## Security Visibility

The monitoring environment provides visibility into several security-relevant activities:

- Successful and failed authentication activity.
- Process creation and command-line arguments.
- Parent-child process relationships.
- PowerShell script execution.
- Account and security group management.
- Kerberos authentication activity.
- Active Directory changes, subject to applicable auditing requirements.
- WMI-related activity.

## Validation Results

Both Windows endpoints successfully synchronized their centralized Wazuh configuration and initialized monitoring of all eight configured event channels.

Security alerts associated with authentication, process creation, Sysmon, PowerShell Script Block Logging, and WMI activity were observed in the Wazuh dashboard.

PowerShell Module Logging was successfully validated locally on both endpoints.

Direct SIEM ingestion of PowerShell Event ID 4103, Task Scheduler events, and Windows Defender events was not independently confirmed during this phase. These sources will be further validated during future detection engineering activities.

Raw event archiving was not enabled, and no custom detection rules were introduced during this phase.

## Documentation

- [Wazuh Agent Deployment](Wazuh-Agent-Deployment.md)
- [Sysmon Integration](Sysmon-Integration.md)
- [Windows Advanced Audit Policies](Windows-Audit-Policies.md)
- [PowerShell Logging](PowerShell-Logging.md)
- [Telemetry Validation](Telemetry-Validation.md)

## Phase Status

**Completed — Initial monitoring objectives achieved.**

The environment is ready to progress to attacker machine deployment and controlled adversary simulations.
