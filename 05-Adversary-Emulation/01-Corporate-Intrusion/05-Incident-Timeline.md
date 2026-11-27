# OP-001 — Incident Timeline

## 1. Overview

This document presents the chronological reconstruction of the activity observed during OP-001 — Corporate Intrusion.

The timeline was reconstructed using endpoint telemetry, centralized Wazuh alerts, and confirmed laboratory operator activity.

The primary objective is to establish relationships between security-relevant events while maintaining a clear distinction between confirmed observations, analytical conclusions, and unverified assumptions.

## 2. Investigation Details

| Attribute | Value |
|---|---|
| Operation | OP-001 — Corporate Intrusion |
| Incident Reference | OP-001 |
| Investigation Date | November 27, 2026 |
| Target Endpoint | CLIENT01 |
| Target IP Address | 10.10.10.30 |
| Associated Remote Host | ATTACKER01 |
| Domain | corp.soclab.test |
| Prototype Version | 1.0 |
| Investigation Status | Preliminary reconstruction |
| Timeline Status | Subject to timestamp validation |

## 3. Evidence Sources

The timeline was reconstructed using the following sources:

| Evidence Source | Relevant Event IDs |
|---|---|
| PowerShell Operational | 4104 |
| Sysmon Operational | 3, 11 |
| Task Scheduler Operational | 100, 106, 140, 200 |
| Windows Security | 4698, 4702 — searched but not observed |
| Wazuh Alerts | 91815, 91826, 92203, 92213 |
| Windows Time Service | Time synchronization diagnostics |
| Laboratory Operator | Confirmed manual troubleshooting activity |

Local endpoint telemetry was particularly important because several relevant events were not represented in the centralized Wazuh alert index.

## 4. Timeline Methodology

The following principles were applied during incident reconstruction:

- Preserve original event timestamps.
- Distinguish event occurrence time from alert indexing time.
- Correlate events using process identifiers and ProcessGuid values.
- Correlate scheduled task events using task instance identifiers.
- Identify associated Windows security contexts.
- Separate confirmed simulated activity from legitimate troubleshooting.
- Document timestamp inconsistencies.
- Avoid treating missing alerts as proof that an activity did not occur.
- Avoid assigning unsupported attack techniques.

All events presented in this document correspond to November 27, 2026.

Windows TimeCreated values are used as the provisional chronological reference for affected network events.

This approach does not establish that these timestamps represent independently verified UTC occurrence times.

## 5. Consolidated Incident Timeline

| Time | Source | Event / Rule | Observed Activity | Classification |
|---|---|---|---|---|
| 19:47:34 | Sysmon | 11 | Initial observed script file creation | Simulated activity |
| 20:02:44 | Sysmon | 11 | Additional script file creation under standard user context | Simulated activity |
| 20:02:47 | Sysmon | 3 | PowerShell-associated UDP communication | Simulated activity — provisional time |
| 20:09:40 | Sysmon | 11 | Repeated script file activity | Simulated activity |
| 20:20:47 | Sysmon | 11 | Script file activity under administrative context | Simulated activity |
| 20:20:48 | Task Scheduler | 106 | Scheduled task registration | Simulated activity |
| 20:20:48 | Task Scheduler | 140 | Scheduled task update | Simulated activity |
| 20:30:24 | Task Scheduler | 100 | Scheduled task started under SYSTEM | Simulated activity |
| 20:30:24 | Task Scheduler | 200 | PowerShell task action launched | Simulated activity |
| 20:30:29 | Sysmon | 3 | SYSTEM-associated UDP communication | Simulated activity — provisional time |
| 20:53:34 | Sysmon / Wazuh | 11 / 92213 | Temporary PowerShell policy-test file creation | Likely benign |
| 20:54:54 | PowerShell / Wazuh | 4104 / 91826 | Prototype script block captured | True positive |
| 20:54:54 | Sysmon / Wazuh | 11 / 92203 | Script file creation or overwrite | True positive |
| 20:56:09 | Sysmon / Wazuh | 11 / 92203 | Repeated script file activity | True positive |
| 20:57:57 | PowerShell / Wazuh | 4104 / 91815 | Manual process identification | Benign positive |
| 20:57:57 | PowerShell / Wazuh | 4104 / 91815 | Additional alert from the same troubleshooting action | Benign positive |
| 21:00:56 | Sysmon / Wazuh | 11 / 92203 | Repeated script file activity | True positive |
| 21:00:59 | Sysmon | 3 | Additional UDP network activity | Simulated activity — provisional time |
| 21:06:29 | Sysmon | 3 | Additional PowerShell-associated UDP activity | Simulated activity — provisional time |
| 21:15:25 | Sysmon / Wazuh | 11 / 92213 | Temporary PowerShell policy-test file creation | Likely benign |
| 21:15:49 | Sysmon / Wazuh | 11 / 92213 | Temporary PowerShell policy-test file creation | Likely benign |
| 21:22:37 | Windows Time Service investigation | N/A | Endpoint time synchronization diagnostics | Analyst activity |

This table contains selected relevant events rather than a complete export of all endpoint activity.

Repeated records and events with identical displayed timestamps are retained where they provide additional investigative context.

## 6. Initial Access Assessment

Initial access was assumed as part of the operation scenario.

The simulated target was the standard domain user CORP\james.miller.

No phishing delivery, malicious attachment execution, vulnerability exploitation, or other initial access mechanism was independently tested.

Consequently, the timeline begins with the earliest relevant endpoint artifact identified during the investigation.

The earliest observed artifact should not be interpreted as the definitive beginning of a compromise.

## 7. Execution Evidence

### 7.1 — Earliest Observed Script Artifact

At approximately 19:47:34, Sysmon recorded file creation activity associated with PowerShell.

Selected evidence:

```text
Event ID: 11
UtcTime: 2026-11-27 19:47:34.836

ProcessGuid:
{043acd20-ddf6-6b09-a804-000000000c00}

ProcessId: 10944

Image:
powershell.exe

TargetFilename:
C:\Scripts\MeuScript.ps1

User:
CORP\james.miller
```

This is the earliest relevant file creation event identified in the collected evidence.

It establishes that the script artifact was observed under the standard domain user context.

It does not independently establish the original delivery mechanism or the precise beginning of script execution.

### 7.2 — Additional Standard User Activity

Additional file activity was recorded under CORP\james.miller.

| Event Time | Process ID | Target Artifact |
|---|---|---|
| 20:02:44.930 | 11156 | MeuScript.ps1 |
| 20:09:40.268 | 11156 | MeuScript.ps1 |

Both events shared the following ProcessGuid:

```text
{043acd20-e1b3-6b09-cf04-000000000c00}
```

The matching ProcessGuid establishes that the two records relate to the same PowerShell process instance.

### 7.3 — Network Activity

Sysmon also recorded UDP network activity associated with Process ID 11156.

The matching ProcessGuid links this network activity to the PowerShell process associated with the script file events.

However, the precise chronological relationship remains provisional because of the timestamp discrepancy affecting Sysmon network records.

## 8. Administrative Context Assessment

Subsequent activity was observed under the administrative account CORP\admin.john.

Relevant file activity occurred at approximately 20:20:47.

Scheduled task registration and update events were recorded at 20:20:48.

The transition between standard user and administrative activity is consistent with the known laboratory procedure, during which administrative privileges were explicitly provided.

No privilege escalation exploit was performed.

The available evidence does not establish that the standard user independently obtained administrative privileges.

## 9. Persistence Evidence

### 9.1 — Task Registration and Update

Task Scheduler Operational logs contained the following events:

| TimeCreated | Event ID | Activity |
|---|---|---|
| 20:20:48 | 106 | Task registered |
| 20:20:48 | 140 | Task updated |

Both events referenced:

```text
\MinhaTaskPowerShell
```

The available records confirm that registration and update activity occurred.

Their exact internal ordering cannot be established from the displayed timestamp precision alone.

### 9.2 — Task Execution

At 20:30:24, Task Scheduler recorded the start of a task instance under SYSTEM.

Selected evidence:

```text
Event ID: 100

Task:
\MinhaTaskPowerShell

Instance ID:
{a6cf23af-1bf1-45fa-b9d2-966c1b01f583}

User:
NT AUTHORITY\SYSTEM
```

A corresponding Event ID 200 recorded the launch of a PowerShell action.

Both events shared the same task instance identifier.

This provides direct evidence linking the task-start and action-start records.

### 9.3 — Associated Network Activity

A Sysmon network event was recorded under the SYSTEM security context.

The event identified PowerShell Process ID 7528.

Its Windows TimeCreated value was approximately five seconds after the Task Scheduler execution records.

The matching security context and associated PowerShell activity support a relationship between the events.

However, the available Task Scheduler records do not establish a direct process identifier match.

The relationship remains a contextual correlation rather than a confirmed process-level linkage.

## 10. Centralized Alert Evidence

### 10.1 — PowerShell Script Block

The initial investigation identified a native Wazuh alert associated with the original prototype script.

```text
Event ID: 4104
EventRecordID: 261851
ProcessID: 6400

SystemTime:
2026-11-27T20:54:54.3265461Z

Wazuh Rule ID: 91826
Wazuh Rule Level: 4
```

The complete script block was captured.

The event provides evidence of script content but does not independently establish that every operation contained in the script was executed.

### 10.2 — Correlated File Creation

A Sysmon file creation event associated with the same PowerShell process was recorded at:

```text
2026-11-27 20:54:54.347
```

The corresponding Wazuh alert used Rule ID 92203 at severity level 6.

The event occurred approximately 21 milliseconds after the PowerShell script block event.

The matching process identifier, host, timing, and associated file behavior provide strong evidence of a relationship between the records.

### 10.3 — Repeated File Activity

Additional alerts were identified at:

| Event Time | Rule ID | Process ID |
|---|---|---|
| 20:56:09.271 | 92203 | 6400 |
| 21:00:56.762 | 92203 | 6400 |

All three file creation alerts shared the same ProcessGuid and target filename.

These records are consistent with repeated activity involving the same PowerShell process and script artifact.

They are not treated as independent compromises.

## 11. Legitimate Troubleshooting Activity

At approximately 20:57:57, two PowerShell alerts were generated by Wazuh Rule 91815.

The laboratory operator confirmed that the activity was associated with manual troubleshooting of a local UDP socket.

| Event Time | Event Record ID | Classification |
|---|---|---|
| 20:57:57.354 | 263796 | Benign Positive |
| 20:57:57.396 | 263797 | Benign Positive |

Both records relate to the same troubleshooting action.

The activity was not initiated through the remote server.

These events are retained in the timeline to distinguish legitimate operator activity from simulated adversarial behavior.

## 12. High-Severity Temporary File Activity

Three additional alerts were generated by Wazuh Rule 92213 at severity level 15.

| Event Time | Process ID | User |
|---|---|---|
| 20:53:34.901 | 6400 | CORP\admin.john |
| 21:15:25.967 | 3504 | CORP\james.miller |
| 21:15:49.830 | 9424 | CORP\admin.john |

The alerts involved temporary files matching the following naming pattern:

```text
__PSScriptPolicyTest_*.ps1
```

The observed characteristics are consistent with legitimate PowerShell application control policy-check activity.

The classification remains preliminary because file content and process ancestry were not independently validated.

The first event shared the same PowerShell process associated with the prototype investigation.

This demonstrates that legitimate runtime activity and suspicious behavior may occur within the same process.

## 13. Timestamp Discrepancy Investigation

### 13.1 — Initial Observation

A significant discrepancy was identified between the Windows TimeCreated field and the embedded Sysmon UtcTime field in multiple network events.

The discrepancy was approximately 14 hours and 28 minutes.

### 13.2 — Observed Timestamp Examples

| Windows TimeCreated | Sysmon UtcTime | Approximate Difference |
|---|---|---|
| 20:30:29 | 06:02:25.007 | 14h 28m 04s |
| 21:00:59 | 06:32:55.442 | 14h 28m 04s |
| 21:06:29 | 06:38:25.930 | 14h 28m 03s |

The discrepancy appears consistent across the selected network records.

The Windows TimeCreated values were displayed with second-level precision, so the calculated differences are approximate.

### 13.3 — Comparison with File Creation Events

The same discrepancy was not observed in the examined Sysmon Event ID 11 records.

For example:

```text
Windows TimeCreated:
2026-11-27 20:54:54

Sysmon UtcTime:
2026-11-27 20:54:54.347
```

These values are consistent within the displayed timestamp precision.

This indicates that the discrepancy was not uniformly present across all examined Sysmon event categories.

### 13.4 — Windows Time Service Findings

The endpoint time configuration was examined during the investigation.

Relevant observations included:

```text
Time Zone:
GMT Standard Time

Leap Indicator:
3 — Not synchronized

Stratum:
1

Source:
VM IC Time Synchronization Provider

Last Successful Sync Time:
November 27, 2026, 21:22:30
```

The endpoint reported an unsynchronized state at the time of inspection.

However, the reported synchronization source and last successful synchronization timestamp do not independently establish the cause of the Sysmon discrepancy.

### 13.5 — Potential Explanations

The following possibilities require further investigation:

- Endpoint time synchronization problems.
- Hyper-V guest time synchronization behavior.
- Historical clock adjustments.
- Virtual machine checkpoint-related time changes.
- Differences in timestamp generation or event recording.

None of these explanations has been confirmed.

The discrepancy cannot be attributed solely to the configured time zone.

### 13.6 — Timeline Handling Decision

The original timestamps are preserved without modification.

Windows TimeCreated is used as a provisional reference for organizing the affected network events.

No fixed offset has been applied to the original evidence.

This decision allows a preliminary reconstruction while preserving the ability to revise the timeline following additional validation.

The issue is recorded as GAP-005.

## 14. Evidence Correlation Summary

| Correlation | Supporting Attributes | Confidence |
|---|---|---|
| Standard user file and network activity | Matching ProcessGuid | High for process identity |
| PowerShell script block and file creation | Matching PID, host, and timestamp | High |
| Task registration and update | Matching task name | High |
| Task start and action execution | Matching task instance ID | High |
| Task execution and SYSTEM network activity | Security context and provisional timing | Moderate |
| Repeated administrative file activity | Matching ProcessGuid and target file | High |
| Temporary policy-test file classification | Filename pattern and process context | Preliminary |
| Precise network chronology | Conflicting timestamp fields | Unresolved |

Confidence levels represent the strength of the available evidence.

They do not represent native SIEM alert severity.

## 15. Investigation Findings

The timeline reconstruction establishes the following findings:

- PowerShell-associated script file activity was observed under a standard domain user.
- Additional activity was observed under an administrative account.
- Scheduled task registration and update were confirmed.
- Scheduled task execution under SYSTEM was confirmed.
- PowerShell-associated UDP communication was observed under multiple security contexts.
- Native Wazuh alerts captured script block and file creation activity.
- Relevant network and scheduled task alerts were not identified in the investigated centralized alert windows.
- Manual troubleshooting generated legitimate process-discovery-related alerts.
- Temporary PowerShell policy-test files generated high-severity alerts.
- Sysmon network records contained significant timestamp inconsistencies.

The investigation does not establish successful arbitrary remote command execution, privilege escalation through exploitation, lateral movement, credential theft, or data exfiltration.

## 16. Investigation Limitations

The following limitations affect the completeness of the reconstructed timeline:

- Initial access was assumed rather than experimentally validated.
- The SIEM was unavailable during part of the initial activity.
- Raw event archiving was disabled.
- Several relevant events were available only through local endpoint logs.
- Windows Security scheduled task auditing was disabled.
- Sysmon network timestamps were inconsistent.
- Direct process-level linkage between the scheduled task action and the SYSTEM network process was not established.
- Arbitrary remote command execution was not tested.
- The legitimacy of the temporary PowerShell policy-test files was not definitively established.

The timeline should therefore be treated as a preliminary reconstruction of the available evidence rather than a complete record of all activity.

## 17. Evidence Preservation

The following evidence preservation principles were applied:

- Original Version 1.0 artifact names were retained.
- Original event timestamps were preserved.
- Relevant event identifiers were documented.
- ProcessGuid and task instance identifiers were retained.
- Selected evidence excerpts were included instead of complete operational scripts.
- Confirmed observations were distinguished from analytical assumptions.
- Missing evidence was explicitly documented.

Version 1.1 demonstration screenshots are maintained separately in the C2 architecture documentation.

They are not incorporated into the original incident chronology.

The repository contains selected evidence excerpts and screenshots rather than a complete forensic acquisition.

Cryptographic evidence hashing and a formal chain-of-custody process were not performed as part of this assessment.

## 18. Conclusion

The preliminary incident timeline demonstrates how endpoint telemetry and centralized SIEM alerts can be combined to reconstruct security-relevant activity.

Process identifiers, ProcessGuid values, task instance identifiers, security contexts, and event timestamps provided valuable correlation opportunities.

The investigation also demonstrated the importance of recognizing legitimate operator activity and distinguishing observed behavior from assumed attack progression.

The timestamp discrepancy remains an important unresolved issue that must be addressed before establishing a definitive network activity chronology.

The findings provide a foundation for the subsequent detection gap analysis and monitoring improvements.

## Related Documentation

- [Operation Overview](01-Operation-Overview.md)
- [C2 Architecture](02-C2-Architecture.md)
- [Execution and Persistence](03-Execution-and-Persistence.md)
- [Native Detection Assessment](04-Native-Detection-Assessment.md)
- [Detection Gap Analysis](06-Detection-Gap-Analysis.md)

---

[Return to OP-001 — Corporate Intrusion](README.md)
