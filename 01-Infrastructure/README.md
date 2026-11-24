# Phase 01 - Infrastructure & SIEM Deployment

## Overview

This phase establishes the foundational infrastructure for the SOC Homelab, including virtualization, network configuration, Linux server deployment, and centralized security monitoring.

**Completion date:** November 24, 2026

**Status:** Completed

## Objectives

- Deploy a dedicated virtual laboratory using Microsoft Hyper-V.
- Configure a dedicated virtual network with NAT connectivity.
- Deploy and configure Ubuntu Server.
- Install Wazuh as a centralized SIEM platform.
- Validate service availability and system performance.
- Implement a checkpoint-based recovery strategy.

## Documentation

| Document | Description |
|---|---|
| Network Architecture | Virtual networking, IP addressing and NAT |
| Hyper-V Deployment | Hypervisor and virtual machine configuration |
| Ubuntu Server | Operating system deployment and configuration |
| Wazuh Deployment | SIEM installation and service validation |
| Troubleshooting | Technical issues and their resolution |
| Performance Validation | Resource utilization and stability testing |

## Implementation Results

- Hyper-V infrastructure successfully deployed.
- Dedicated SOC-LAB virtual network configured.
- Static IP addressing and NAT connectivity validated.
- Ubuntu Server 24.04.5 LTS successfully installed.
- SSH remote administration established.
- Wazuh All-in-One successfully deployed.
- All four Wazuh services confirmed operational.
- Initial dashboard responsiveness validated.
- Virtual machine recovery checkpoints created.

## Performance Baseline

| Metric | Result |
|---|---|
| Assigned RAM | 8 GB |
| Available RAM | 5.3 GiB |
| Virtual CPUs | 4 |
| Load Average (1 minute) | 0.04 |
| Available Storage | 69 GB |
| Swap Usage | 12 KiB |
| Dashboard Responsiveness | No noticeable delays |

These measurements represent the initial baseline before endpoint integration and security event ingestion.

## Recovery Checkpoints

| Checkpoint | Purpose |
|---|---|
| 01 - Ubuntu Clean Installation | Clean and updated operating system |
| 02 - Wazuh Clean Installation | Operational SIEM deployment |

## Key Lessons Learned

- Hypervisor compatibility can significantly affect virtual machine performance.
- Dynamic memory allocation should be evaluated carefully for resource-intensive services.
- Network connectivity and DNS resolution should be validated before deploying security tools.
- Establishing a performance baseline helps identify future resource bottlenecks.
- Checkpoints provide useful recovery points during infrastructure deployment.

## Next Phase

Active Directory deployment and Windows endpoint integration.
