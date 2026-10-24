# Splunk Index Configuration

## Windows Telemetry Index

The lab uses a dedicated events index:

```text
windows
```

The Universal Forwarder inputs assign both Windows Security and Sysmon telemetry to this index.

## Data Sources

| Source | Sourcetype | Index |
|---|---|---|
| Windows Security | `WinEventLog:Security` | `windows` |
| Sysmon Operational | `XmlWinEventLog:Microsoft-Windows-Sysmon/Operational` | `windows` |

## Validation

In Splunk Search & Reporting:

```spl
index=windows | stats count by sourcetype
```

A healthy pipeline should show both expected sourcetypes once the endpoint is configured and generating telemetry.
