# Phase 04 — Attacker Machine Deployment

## Overview

This phase introduces a dedicated Kali Linux virtual machine to perform controlled adversary simulations against the SOC homelab infrastructure.

The attacker machine will generate security-relevant activity to support detection engineering, threat hunting, incident investigation, and incident response exercises.

## Objectives

- Deploy a dedicated adversary simulation workstation.
- Integrate Kali Linux into the existing lab network.
- Validate internal connectivity and DNS resolution.
- Verify the availability of security testing tools.
- Establish security boundaries for controlled simulations.
- Create a clean recovery checkpoint.

## Documentation

| Document | Description |
|---|---|
| [Kali Linux Deployment](01-Kali-Linux-Deployment.md) | Virtual machine configuration, network validation, security tools, and recovery checkpoint. |

## Phase Status

- [x] Kali Linux ISO Download
- [x] Hyper-V Virtual Machine Creation
- [x] Kali Linux Installation
- [x] Network Configuration
- [x] Security Tools Validation
- [x] Network Architecture and Security Boundaries
- [x] Baseline Checkpoint
- [x] Documentation

## Next Phase

Phase 05 — Controlled Adversary Simulations.

The next phase focuses on generating controlled attack activity against authorized lab systems and analyzing the resulting security telemetry.
