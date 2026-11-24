
# Infrastructure Troubleshooting

## 1. Overview

This document describes the technical challenges encountered during the deployment of the SOC Homelab infrastructure.

Each case follows a structured troubleshooting methodology focused on identifying symptoms, collecting evidence, evaluating potential root causes, implementing corrective actions, and validating the results.

## 2. Troubleshooting Methodology

The following methodology was applied throughout the infrastructure deployment:

1. Identify the problem.
2. Collect technical evidence.
3. Analyze potential root causes.
4. Evaluate available solutions.
5. Implement corrective actions.
6. Validate the results.
7. Document findings and lessons learned.

## 3. Issue Summary

| ID | Issue | Impact | Status |
|---|---|---|---|
| INF-001 | VirtualBox performance degradation | High | Resolved through migration |
| INF-002 | Insufficient Hyper-V memory allocation | High | Resolved |
| INF-003 | Automatic network configuration failure | Medium | Resolved |
| INF-004 | Gateway ICMP response failure | Low | Connectivity validated |
| INF-005 | Ubuntu installation media unmount failure | Low | Resolved |
| INF-006 | Default LVM storage allocation | Medium | Adjusted |
| INF-007 | Virtual console clipboard limitations | Low | Workaround implemented |

Impact classifications reflect the effect on laboratory deployment rather than formal security incident severity.

---

## 4. INF-001 - VirtualBox Performance Degradation

### Problem Description

The initial SOC Homelab was deployed using Oracle VirtualBox.

The Wazuh environment experienced significant performance degradation, affecting virtual machine responsiveness and Dashboard accessibility.

### Observed Symptoms

- Slow virtual machine operation.
- Delayed Wazuh Dashboard loading.
- Authentication and connection timeouts.
- Poor administrative experience.

### Evidence Collection

VirtualBox logs were inspected and contained the following entries:

```text
NEM: WHvCapabilityCodeHypervisorPresent is TRUE
NEM: NEMR3Init: Snail execution mode is active!
```

These entries indicated that VirtualBox was operating through the Windows hypervisor compatibility layer.

### Root Cause Analysis

The Windows hypervisor was active despite the Hyper-V optional feature initially being disabled.

Windows virtualization-based security features were investigated as potential contributing factors.

The VirtualBox compatibility layer was identified as a likely contributor to the observed performance degradation.

However, the exact root cause was not conclusively established through controlled benchmarking.

### Resolution

Rather than permanently disabling Windows security protections, the laboratory was rebuilt using Microsoft Hyper-V.

The following host security protections were preserved or restored:

- Secure Boot.
- Virtualization-Based Security.
- Memory Integrity.
- Microsoft Defender Antivirus.

A fresh Ubuntu Server virtual machine was deployed, followed by a new Wazuh All-in-One installation.

### Validation

Following the migration:

- Wazuh Dashboard authentication succeeded.
- Dashboard responsiveness improved.
- All central Wazuh services were operational.
- Previously observed delays were no longer noticeable during initial testing.

### Outcome

The migration resolved the observed usability problems during initial testing.

No quantitative comparison between the two hypervisors was performed.

### Lessons Learned

- Hypervisor compatibility should be considered during performance investigations.
- A disabled Hyper-V optional feature does not necessarily indicate that the Windows hypervisor is inactive.
- Host security protections should not be unnecessarily weakened to accommodate virtualization software.
- Observed improvements should be distinguished from experimentally proven root causes.

**Status: Resolved through infrastructure migration**

---

## 5. INF-002 - Insufficient Hyper-V Memory Allocation

### Problem Description

The SOC-SIEM virtual machine was configured with 8192 MB of startup memory.

However, Ubuntu reported significantly less physical memory than expected.

### Evidence Collection

The following command was executed:

```bash
free -h
```

Initial observed output:

```text
               total        used        free      shared  buff/cache   available
Mem:           573Mi       424Mi       175Mi       3.9Mi       207Mi       148Mi
Swap:          2.0Gi       268Ki       2.0Gi
```

The operating system recognized only 573 MiB of total memory.

### Root Cause Analysis

Inspection of the Hyper-V virtual machine settings revealed the following configuration:

| Parameter | Original Configuration |
|---|---|
| Startup Memory | 8192 MB |
| Dynamic Memory | Enabled |
| Minimum Memory | 512 MB |
| Maximum Memory | 1048576 MB |
| Memory Buffer | 20% |

Hyper-V Dynamic Memory allowed the memory assigned to the guest operating system to decrease according to workload demand.

The configured startup memory did not represent a permanently reserved allocation.

### Resolution

The following corrective actions were performed:

1. Shut down the SOC-SIEM virtual machine.
2. Open the virtual machine memory settings.
3. Disable Dynamic Memory.
4. Configure a fixed memory allocation of 8192 MB.
5. Restart the virtual machine.
6. Validate the recognized memory capacity.

### Validation

The memory configuration was checked again:

```bash
free -h
```

Observed output:

```text
               total        used        free      shared  buff/cache   available
Mem:           7.8Gi       421Mi       7.4Gi       3.9Mi       213Mi       7.3Gi
Swap:          2.0Gi          0B       2.0Gi
```

### Before and After Comparison

| Metric | Before | After |
|---|---|---|
| Total Recognized Memory | 573 MiB | 7.8 GiB |
| Available Memory | 148 MiB | 7.3 GiB |
| Dynamic Memory | Enabled | Disabled |
| Memory Allocation | Variable | Fixed |

### Outcome

Ubuntu successfully recognized the expected memory capacity.

The virtual machine was subsequently able to host the Wazuh All-in-One deployment.

### Lessons Learned

- Startup memory is not equivalent to permanently assigned memory when Dynamic Memory is enabled.
- Resource allocation should be validated from within the guest operating system.
- Fixed memory allocation provides predictable resources for this SIEM deployment.
- Dynamic Memory is not inherently defective, but its configuration must match workload requirements.

**Status: Resolved**

---

## 6. INF-003 - Automatic Network Configuration Failure

### Problem Description

During the Ubuntu Server installation, automatic network configuration failed.

The installer displayed the following message:

```text
eth0
disabled autoconfiguration failed
```

### Root Cause Analysis

The SOC-LAB network was implemented using a Hyper-V Internal Virtual Switch and Windows NAT.

The custom NAT configuration did not provide a DHCP service.

Consequently, Ubuntu could not automatically obtain an IPv4 address.

### Resolution

The network interface was manually configured using static IPv4 addressing.

| Parameter | Configuration |
|---|---|
| Interface | eth0 |
| IP Address | 10.10.10.20 |
| Prefix Length | 24 |
| Gateway | 10.10.10.1 |

### Validation

```bash
ip -br a
ip route
ping -c 4 8.8.8.8
ping -c 4 google.com
```

The following results were observed:

- Static IPv4 address successfully configured.
- Default route correctly configured.
- Outbound internet connectivity established.
- DNS resolution operational.

### Outcome

The Ubuntu installation proceeded successfully with manual network configuration.

### Lessons Learned

- Windows NAT does not automatically provide DHCP services.
- Static addressing is appropriate for infrastructure components requiring predictable network locations.
- Network configuration should be validated before deploying security applications.

**Status: Resolved**

---

## 7. INF-004 - Gateway ICMP Response Failure

### Problem Description

Following Ubuntu Server installation, the configured default gateway did not respond to ICMP echo requests.

### Evidence Collection

```bash
ping -c 4 10.10.10.1
```

Observed result:

```text
4 packets transmitted, 0 received, 100% packet loss
```

The routing table was inspected:

```bash
ip route
```

Observed output:

```text
default via 10.10.10.1 dev eth0 proto static
10.10.10.0/24 dev eth0 proto kernel scope link src 10.10.10.20
```

### Additional Connectivity Tests

```bash
ping -c 4 8.8.8.8
ping -c 4 google.com
```

| Test | Packet Loss | Average Latency |
|---|---|---|
| Default Gateway | 100% | N/A |
| External IP Address | 0% | 10.427 ms |
| External Domain | 0% | 10.595 ms |

SSH access from the Windows host was also successful.

### Root Cause Analysis

The successful external connectivity tests demonstrated that the gateway and NAT configuration were forwarding traffic.

Inbound ICMP filtering by the Windows host firewall was considered the most likely explanation for the missing gateway response.

This hypothesis was not independently verified.

### Resolution

No firewall configuration changes were required.

The existing network configuration was retained because the required connectivity was operational.

### Outcome

The gateway remained unresponsive to ICMP echo requests during the recorded test, but the required network functionality was successfully validated.

### Lessons Learned

- A failed ping does not necessarily indicate a network connectivity failure.
- ICMP filtering must be considered during network troubleshooting.
- Multiple connectivity tests should be used before concluding that a network component is unavailable.
- Security controls should not be disabled solely to obtain a successful ping response.

**Status: Connectivity validated; ICMP behavior retained**

---

## 8. INF-005 - Ubuntu Installation Media Unmount Failure

### Problem Description

After completing the Ubuntu installation, the virtual machine displayed an installation media unmount error during restart.

### Evidence Collection

```text
[FAILED] Failed unmounting cdrom.mount - /cdrom.
Please remove the installation medium, then press ENTER:
```

### Root Cause Analysis

The error occurred while the installer was attempting to unmount the virtual installation media.

The exact reason for the unmount failure was not independently established.

### Resolution

The following actions were performed:

1. Open the virtual machine settings in Hyper-V Manager.
2. Locate the virtual DVD drive.
3. Detach the Ubuntu installation ISO.
4. Return to the virtual machine console.
5. Complete the restart.

### Validation

Ubuntu successfully booted from the virtual hard disk.

Authentication using the configured administrative account was successful.

### Outcome

The operating system installation was completed without requiring reinstallation.

### Lessons Learned

- Virtual installation media should be detached after operating system installation.
- Installation media errors do not necessarily indicate operating system corruption.
- Virtual firmware and storage configuration should be reviewed when troubleshooting boot issues.

**Status: Resolved**

---

## 9. INF-006 - Default LVM Storage Allocation

### Problem Description

The Ubuntu installer initially allocated approximately half of the available LVM volume group capacity to the root filesystem.

### Evidence Collection

| Component | Initial Capacity |
|---|---|
| Virtual Disk | 100 GiB |
| LVM Volume Group | 96.945 GiB |
| Root Logical Volume | 48.472 GiB |
| Unallocated LVM Capacity | 48.472 GiB |

### Analysis

The default allocation preserved unused capacity within the LVM volume group.

Although this was not an installation error, it did not match the preferred storage configuration for the initial SIEM deployment.

Wazuh requires storage for security alerts, indexed events, logs, and other platform data.

### Resolution

The root logical volume was manually expanded during the Ubuntu installation.

| Component | Final Capacity |
|---|---|
| Root Logical Volume | 96.945 GiB |
| /boot | 2 GiB |
| /boot/efi | 1.049 GiB |

### Validation

```bash
df -h /
```

Observed output:

```text
Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv   95G  4.7G   86G   6% /
```

### Outcome

The root filesystem was configured to use the available LVM volume group capacity.

Future storage expansion may require increasing the virtual disk capacity or adding additional storage.

### Lessons Learned

- Default partitioning should be reviewed before deploying storage-intensive applications.
- Unallocated LVM capacity is not necessarily wasted storage.
- Storage allocation should reflect application requirements and future expansion plans.
- SIEM storage consumption should be monitored as endpoint telemetry increases.

**Status: Configuration adjusted**

---

## 10. INF-007 - Virtual Console Clipboard Limitations

### Problem Description

Copying and pasting commands directly into the Ubuntu Server console through Hyper-V was inconvenient.

This affected administrative efficiency and increased the possibility of manual command entry errors.

### Analysis

The basic Hyper-V virtual machine console does not provide the same clipboard integration as a conventional desktop terminal session.

Ubuntu Server was deployed without a graphical desktop environment.

### Resolution

OpenSSH was used as the primary remote administration method.

From Windows PowerShell:

```powershell
ssh socadmin@10.10.10.20
```

The SSH service was validated using:

```bash
systemctl status ssh --no-pager
```

### Validation

- SSH authentication succeeded.
- Remote command execution was operational.
- Commands could be copied and pasted through the Windows terminal.
- Administrative access was established without requiring a graphical desktop environment.

### Outcome

SSH provided an efficient alternative to direct virtual console administration.

### Lessons Learned

- Remote administration is preferable for headless Linux servers.
- SSH simplifies repetitive command execution and evidence collection.
- Graphical desktop environments are not required for effective Linux server administration.

**Status: Workaround implemented**

---

## 11. Final Infrastructure Validation

Following the troubleshooting and deployment activities, the infrastructure was validated.

| Component | Final Observed Status |
|---|---|
| Hyper-V | Operational |
| SOC-LAB Virtual Switch | Operational |
| NAT Connectivity | Operational |
| Ubuntu Server | Operational |
| Memory Allocation | Validated |
| Storage Configuration | Validated |
| SSH Administration | Operational |
| Wazuh Manager | Active |
| Wazuh Indexer | Active |
| Wazuh Dashboard | Active |
| Filebeat | Active |
| Recovery Checkpoints | Created |

## 12. Key Technical Takeaways

The infrastructure deployment demonstrated the importance of:

- Evidence-based troubleshooting.
- Distinguishing symptoms from root causes.
- Validating assumptions through independent tests.
- Preserving host security protections.
- Reviewing resource allocation before application deployment.
- Establishing performance baselines.
- Implementing recovery points.
- Documenting technical decisions and their outcomes.

## 13. Related Documentation

- [Network Architecture](Network-Architecture.md)
- [Hyper-V Deployment](Hyper-V-Deployment.md)
- [Ubuntu Server Deployment](Ubuntu-Server.md)
- [Wazuh Deployment](Wazuh-Deployment.md)

## 14. Conclusion

The infrastructure challenges encountered during the SOC Homelab deployment provided practical experience in virtualization, Linux administration, network troubleshooting, resource management, and technical problem resolution.

The resulting infrastructure provides a functional baseline for the next project phase.

Further validation will be performed as Active Directory, monitored endpoints, and security telemetry are introduced.
