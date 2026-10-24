# Sysmon Setup

## Purpose

Sysmon extends Windows endpoint visibility beyond the standard Security log by recording process, network, DNS, file, and registry activity.

## Lab Configuration

- Sysmon version: 18
- Schema: 4.91
- Configuration path: `C:\Windows\System32\soc-sysmon.xml`
- Hash algorithm: SHA256
- Configuration SHA256: `37365A8E9D08E1FDDD2CB5BF475AB493F4B30FC4FAD5C5DE2C77767D4BAF059A`

## Enabled Telemetry

```text
ProcessCreate
NetworkConnect
DnsQuery
FileCreate (selected directories)
RegistryEvent (selected persistence-related paths)
```

Selected file paths include Downloads, Temp, AppData, and Startup. Selected registry paths include Run, RunOnce, Services, Winlogon, and Image File Execution Options.

## Apply Configuration

```powershell
sysmon -c C:\Windows\System32\soc-sysmon.xml
```

## Local Validation

```powershell
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" -MaxEvents 5 | Select-Object TimeCreated, Id, ProviderName
```

Important event IDs for the lab:

| Event ID | Meaning |
|---:|---|
| 1 | Process Create |
| 3 | Network Connection |
| 4 | Sysmon service state change |
| 5 | Process Terminated |
| 11 | File Create |
| 12–14 | Registry events |
| 16 | Sysmon configuration state |
| 22 | DNS Query |

## Forwarding Permission Requirement

The Splunk Universal Forwarder service runs as `NT SERVICE\SplunkForwarder`. Access to the Sysmon event channel requires membership in the Windows **Event Log Readers** group in this lab.

The permission issue and its resolution are documented in [`troubleshooting/sysmon-forwarding.md`](../troubleshooting/sysmon-forwarding.md).
