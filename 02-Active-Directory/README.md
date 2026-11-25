
# Phase 02 - Active Directory & Endpoint Deployment

## Overview

This phase focuses on building a simulated corporate Active Directory environment and deploying a domain-joined Windows endpoint.

The environment provides the foundation for security monitoring, attack simulations, detection engineering, threat hunting, and incident response investigations.

## Objectives

- Deploy a Windows Server 2022 Domain Controller.
- Configure Active Directory Domain Services and DNS.
- Establish a corporate organizational structure.
- Create standard, privileged, and service accounts.
- Implement role separation and least privilege principles.
- Deploy a Windows 11 workstation.
- Join the workstation to the Active Directory domain.
- Validate domain authentication and network connectivity.

## Environment

| Component | Configuration |
|---|---|
| Domain | corp.soclab.test |
| NetBIOS Name | CORP |
| Domain Controller | DC01 |
| Domain Controller IP | 10.10.10.10 |
| Operating System | Windows Server 2022 |
| Workstation | CLIENT01 |
| Workstation IP | 10.10.10.30 |
| Workstation OS | Windows 11 Pro |
| SIEM | Wazuh |
| SIEM IP | 10.10.10.20 |
| Network | 10.10.10.0/24 |

## Implementation Status

- [x] Active Directory Domain Services deployment
- [x] DNS configuration and validation
- [x] Organizational Units creation
- [x] Security Groups creation
- [x] Corporate user accounts creation
- [x] Privileged account separation
- [x] Service account creation
- [x] Windows 11 deployment
- [x] Active Directory domain join
- [x] Domain user authentication
- [x] Workstation OU validation

## Documentation

- [Active Directory Deployment](Active-Directory-Deployment.md)
- [Corporate Structure](Corporate-Structure.md)
- [Endpoint Deployment](Endpoint-Deployment.md)

## Outcome

A functional Active Directory environment was successfully deployed and validated, including a domain-joined Windows 11 workstation, departmental security groups, six standard users, one additional privileged account, and two service accounts.

The service accounts are provisioned but are not yet associated with operational services.

This environment will serve as the foundation for endpoint telemetry collection, security monitoring, and controlled adversary simulations.

## Next Phase

Phase 03 - Security Monitoring & Telemetry Collection

The next phase will focus on Wazuh agent deployment, Sysmon configuration, Windows auditing, and security event collection.
  
