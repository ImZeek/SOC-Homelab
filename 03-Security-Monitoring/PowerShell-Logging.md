# PowerShell Logging

## Objective

Enhance visibility into PowerShell execution across the corporate Active Directory environment by enabling advanced logging capabilities through centralized Group Policy.

PowerShell is a legitimate administrative tool that can also be abused by adversaries for command execution, reconnaissance, persistence, lateral movement, and defense evasion.

## Group Policy Configuration

A dedicated Group Policy Object was created to centrally manage PowerShell logging.

| Component | Configuration |
|---|---|
| GPO Name | SOC - PowerShell Logging |
| Target OU | Domain Controllers |
| Target OU | CORP-Workstations |
| Monitored Endpoints | DC01 and CLIENT01 |

The following policies were configured:

| Policy | Configuration |
|---|---|
| Turn on Module Logging | Enabled |
| Module Names | * |
| Turn on PowerShell Script Block Logging | Enabled |
| Script Block Invocation Logging | Disabled |
| PowerShell Transcription | Not Configured |
| Include command line in process creation events | Enabled |

PowerShell Transcription was not enabled during this phase to limit unnecessary storage consumption and avoid additional logging complexity.

## Security Telemetry

### PowerShell Module Logging — Event ID 4103

Module Logging records PowerShell pipeline execution details, including command invocation and parameter binding information for monitored modules.

This telemetry supports the investigation of:

- Suspicious PowerShell commands.
- Administrative command execution.
- Reconnaissance activity.
- Account and system enumeration.
- Potential misuse of legitimate administrative tools.

### PowerShell Script Block Logging — Event ID 4104

Script Block Logging records the contents of PowerShell script blocks processed by the PowerShell engine.

This provides visibility into:

- Suspicious script execution.
- Encoded and obfuscated PowerShell activity.
- Payload execution.
- Download commands.
- Fileless attack techniques.

### Windows Process Creation — Event ID 4688

Process Creation auditing was enhanced by enabling command-line logging.

This provides additional execution context, including process names, command-line arguments, parent process information, and security principals.

Combined with Sysmon Event ID 1, this telemetry supports process correlation and incident reconstruction.

## Configuration Validation

Effective policy settings were verified through the Windows Registry on both endpoints.

| Registry Value | Expected Value | Result |
|---|---|---|
| EnableModuleLogging | 1 | Passed |
| EnableScriptBlockLogging | 1 | Passed |
| ProcessCreationIncludeCmdLine_Enabled | 1 | Passed |

Controlled execution of legitimate PowerShell commands was performed to validate event generation.

```powershell
Get-Process | Select-Object -First 5
Get-Service | Select-Object -First 5
whoami.exe /all
```

Both endpoints successfully generated PowerShell Event IDs 4103 and 4104, as well as Windows Security Event ID 4688 containing command-line information.

## Wazuh Integration

The PowerShell Operational event channel was added to the centralized Wazuh agent configuration.

```xml
<localfile>
  <location>Microsoft-Windows-PowerShell/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

Both agents successfully synchronized their configuration and initialized monitoring of the PowerShell Operational channel.

The following queries were used to validate security visibility within Wazuh Threat Hunting.

### Script Block Logging

```
data.win.system.eventID: "4104"
```

### Module Logging

```
data.win.system.eventID: "4103"
```

### Windows Process Creation

```
data.win.system.eventID: "4688"
```

## Validation Results

| Telemetry | Local Event Generation | SIEM Alert Visibility |
|---|---|---|
| PowerShell Module Logging — 4103 | Confirmed | Not confirmed |
| PowerShell Script Block Logging — 4104 | Confirmed | Confirmed |
| Windows Process Creation — 4688 | Confirmed | Confirmed |

Although Event ID 4103 was successfully generated on both endpoints, no corresponding alerts were observed in Wazuh Threat Hunting during the validation period.

The absence of indexed alerts does not necessarily indicate a telemetry collection failure.

Raw event archiving was not enabled, and no custom detection rules were introduced to validate this event type. Direct ingestion verification was deferred to future detection engineering activities.

## MITRE ATT&CK Relevance

PowerShell logging provides telemetry that can support the investigation of several adversary techniques.

| Technique ID | Technique |
|---|---|
| T1059.001 | PowerShell |
| T1027 | Obfuscated Files or Information |
| T1105 | Ingress Tool Transfer |
| T1082 | System Information Discovery |
| T1057 | Process Discovery |
| T1087 | Account Discovery |

These mappings represent potential detection and investigation use cases rather than validated attack detections.

## Security Considerations

PowerShell logs and process command-line arguments may contain sensitive information, including credentials, tokens, and administrative parameters.

Access to collected telemetry should therefore be restricted to authorized personnel.

## Conclusion

Advanced PowerShell logging was successfully implemented across the corporate environment using centralized Group Policy.

Local event generation and SIEM visibility for script block and process creation activity were validated.

This configuration establishes a foundation for future PowerShell-based adversary simulations, behavioral detection engineering, threat hunting, and incident response.
