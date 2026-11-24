# Ubuntu Server Deployment

## 1. Overview

Ubuntu Server was deployed as the underlying operating system for the Wazuh All-in-One platform.

The server provides a dedicated environment for centralized security monitoring, event processing, alert generation, and security data storage.

The deployment was performed on Microsoft Hyper-V using a dedicated virtual machine connected to the SOC-LAB network.

## 2. Operating System Selection

Ubuntu Server 24.04.5 LTS was selected for the following reasons:

- Long-term support and security updates.
- Compatibility with Wazuh.
- Low operating system resource overhead.
- Native support for Linux administration tools.
- Remote administration through OpenSSH.
- Extensive technical documentation and community support.

A server installation without a graphical desktop environment was selected to minimize unnecessary resource consumption.

## 3. Server Specifications

| Parameter | Configuration |
|---|---|
| Virtual Machine | SOC-SIEM |
| Hostname | soc-siem |
| Operating System | Ubuntu Server 24.04.5 LTS |
| Codename | Noble |
| Architecture | AMD64 |
| Assigned RAM | 8 GB |
| Virtual CPUs | 4 |
| Virtual Disk | 100 GB |
| Boot Mode | UEFI |
| Secure Boot | Enabled |
| Network Interface | eth0 |
| IP Address | 10.10.10.20/24 |
| Default Gateway | 10.10.10.1 |
| Administrative User | socadmin |

## 4. Installation Process

The Ubuntu Server installation ISO was attached to the virtual DVD drive of the SOC-SIEM virtual machine.

The installation included the following configuration steps:

1. Select Ubuntu Server installation.
2. Configure the network interface manually.
3. Configure disk partitioning using LVM.
4. Create the server hostname and administrative account.
5. Enable OpenSSH Server.
6. Complete the operating system installation.
7. Detach the installation ISO.
8. Restart the virtual machine.
9. Authenticate using the configured administrative account.

No graphical desktop environment was installed.

## 5. Network Configuration

The virtual machine was connected to the dedicated SOC-LAB internal virtual switch.

Since the custom Hyper-V NAT network does not provide DHCP services, static IPv4 addressing was configured manually.

| Parameter | Value |
|---|---|
| Interface | eth0 |
| IPv4 Method | Manual |
| Subnet | 10.10.10.0/24 |
| IP Address | 10.10.10.20 |
| Gateway | 10.10.10.1 |
| Subnet Mask | 255.255.255.0 |

Network connectivity was validated after installation.

```bash
ip -br a
ip route
ping -c 4 8.8.8.8
ping -c 4 google.com
```

Both internet connectivity and DNS resolution were successfully validated.

Detailed network configuration and connectivity results are available in [Network Architecture](Network-Architecture.md).

## 6. Storage Configuration

Logical Volume Management (LVM) was selected to provide flexible storage management.

The Ubuntu installer initially allocated approximately half of the available volume group capacity to the root logical volume.

The root logical volume was manually expanded during installation to use the remaining available capacity.

### Final Partition Layout

| Mount Point | Size | Filesystem |
|---|---|---|
| / | 96.945 GiB | ext4 |
| /boot | 2 GiB | ext4 |
| /boot/efi | 1.049 GiB | FAT32 |

### LVM Configuration

| Component | Name |
|---|---|
| Volume Group | ubuntu-vg |
| Logical Volume | ubuntu-lv |
| Mount Point | / |

Storage utilization was validated using:

```bash
df -h /
```

Initial observed output:

```text
Filesystem                         Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv   95G  4.7G   86G   6% /
```

The available storage capacity was considered sufficient for the initial Wazuh deployment.

Future storage requirements will depend on event ingestion volume, index retention policies, and the number of monitored endpoints.

## 7. Installation Media Troubleshooting

During the first restart, Ubuntu displayed the following message:

```text
[FAILED] Failed unmounting cdrom.mount - /cdrom.
Please remove the installation medium, then press ENTER:
```

The installation ISO was detached from the virtual DVD drive through Hyper-V Manager.

The restart was then completed successfully.

No operating system reinstallation was required.

## 8. Remote Administration

OpenSSH Server was enabled during the Ubuntu installation to support remote administration from the Windows host.

The SSH service was validated using:

```bash
systemctl status ssh --no-pager
```

Observed results:

- SSH service active and running.
- TCP port 22 listening.
- Successful authentication from the Windows host.
- Remote terminal access established.

The following command was used from Windows PowerShell:

```powershell
ssh socadmin@10.10.10.20
```

SSH became the primary administration method for the Ubuntu server.

This approach also resolved the limitations associated with copying and pasting commands through the Hyper-V virtual machine console.

Password-based authentication was used during the initial deployment. SSH key-based authentication and additional hardening remain future improvements.

## 9. System Validation

### 9.1 Operating System

```bash
lsb_release -a
```

Observed output:

```text
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.5 LTS
Release:        24.04
Codename:       noble
```

### 9.2 CPU Validation

```bash
nproc
```

Observed output:

```text
4
```

All four assigned virtual processors were recognized by the operating system.

### 9.3 Memory Validation

```bash
free -h
```

Initial validation revealed that Ubuntu recognized only 573 MiB of total memory.

Further investigation identified Hyper-V Dynamic Memory as the cause of the reduced memory allocation.

Dynamic Memory was disabled, and the virtual machine was restarted with a fixed allocation of 8192 MB.

Observed output after correction:

```text
               total        used        free      shared  buff/cache   available
Mem:           7.8Gi       421Mi       7.4Gi       3.9Mi       213Mi       7.3Gi
Swap:          2.0Gi          0B       2.0Gi
```

The corrected memory allocation provided sufficient resources for the initial Wazuh deployment.

Additional virtualization details are available in [Hyper-V Deployment](Hyper-V-Deployment.md).

## 10. System Updates

The operating system package repositories were refreshed, and available package upgrades were installed.

```bash
sudo apt update && sudo apt upgrade -y
```

Additional administration and troubleshooting utilities were installed:

```bash
sudo apt install -y curl wget unzip htop net-tools dnsutils
```

| Utility | Purpose |
|---|---|
| curl | HTTP requests and API testing |
| wget | File and package downloads |
| unzip | Archive extraction |
| htop | Interactive resource monitoring |
| net-tools | Legacy network administration utilities |
| dnsutils | DNS resolution and troubleshooting |

The server was restarted following the update process.

```bash
sudo reboot
```

SSH connectivity was subsequently re-established.

## 11. Pre-SIEM Performance Baseline

The following measurements were recorded before installing Wazuh.

| Metric | Observed Result |
|---|---|
| Total Memory | 7.8 GiB |
| Used Memory | 421 MiB |
| Available Memory | 7.3 GiB |
| Swap Capacity | 2 GiB |
| Swap Usage | 0 B |
| Virtual CPUs | 4 |
| Root Filesystem Capacity | 95 GiB |
| Available Storage | 86 GiB |
| Internet Connectivity | Operational |
| DNS Resolution | Operational |
| SSH Access | Operational |

These measurements provide a reference point for evaluating resource consumption after deploying the SIEM platform.

## 12. Security Considerations

The initial deployment incorporated the following security considerations:

- Secure Boot enabled at the virtual machine level.
- Dedicated virtual laboratory network.
- No direct bridging to the physical network.
- Administrative access through a dedicated user account.
- Elevated privileges obtained through sudo.
- Operating system package updates.
- Remote administration through encrypted SSH connections.
- Credentials excluded from project documentation.

The initial deployment is not intended to represent a fully hardened production server.

Future security improvements include:

- SSH key-based authentication.
- Review of password-based authentication.
- Host firewall configuration.
- Service exposure review.
- Security configuration assessment.
- Network isolation before adversary simulations.

## 13. Recovery Checkpoint

After completing the operating system installation, configuration, updates, and validation, a Hyper-V checkpoint was created.

Checkpoint name:

```text
01 - Ubuntu Clean Installation
```

This checkpoint provides a recovery point before installing the Wazuh platform.

## 14. Deployment Validation

| Validation | Result |
|---|---|
| Operating System Installation | Passed |
| Static IP Configuration | Passed |
| Default Route Configuration | Passed |
| Internet Connectivity | Passed |
| DNS Resolution | Passed |
| CPU Allocation | Passed |
| Memory Allocation | Passed after correction |
| Storage Configuration | Passed |
| SSH Remote Administration | Passed |
| System Updates | Completed |
| Recovery Checkpoint | Created |

## 15. Key Lessons Learned

- Static IP addressing is important for centralized security infrastructure.
- Default LVM allocation should be reviewed before deploying storage-intensive applications.
- Dynamic Memory can significantly reduce the memory currently assigned to a virtual machine.
- Resource validation should be performed before application deployment.
- SSH provides an efficient administration method for headless Linux servers.
- Establishing a pre-deployment baseline helps identify future performance bottlenecks.
- Recovery checkpoints simplify laboratory troubleshooting and rollback.

## 16. Conclusion

Ubuntu Server was successfully deployed, configured, and validated as the operating system for the SOC-SIEM virtual machine.

Network connectivity, remote administration, storage capacity, and resource allocation were verified before proceeding with the Wazuh installation.

The server provides the foundation for centralized security monitoring within the SOC Homelab.
