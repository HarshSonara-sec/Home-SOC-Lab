# Detection: Repeated Windows Failed Logons

## Objective

Identify repeated Windows authentication failures that may represent user error, stale credentials, password spraying, brute-force activity, or another authentication problem requiring triage.

## Data Source

- Index: `windows`
- Sourcetype: `WinEventLog:Security`
- Event ID: `4625`

## Base Search

```spl
index=windows sourcetype="WinEventLog:Security" EventCode=4625
```

Use the base search in Splunk Web to inspect the raw events and available fields before applying thresholds.

## Threshold Search

```spl
index=windows sourcetype="WinEventLog:Security" EventCode=4625
| bin _time span=5m
| stats count as failed_logins by _time, Account_Name, Source_Network_Address
| where failed_logins >= 3
| sort - _time
```

## Why It Matters

A single failed logon is usually weak evidence. Repeated failures over a short interval provide stronger signal and justify investigation, especially when the same source targets multiple accounts or when a successful authentication follows a burst of failures.

## Analyst Context

Review:

- Account name
- Source network address
- Logon type
- Failure reason
- Status and sub-status
- Number and timing of attempts
- Whether multiple accounts were targeted
- Whether successful authentication followed
- Related endpoint and process telemetry

## Triage Guidance

Do not classify every 4625 burst as brute force. Validate the user, source, timing, expected activity, and related authentication events before escalation.

## ATT&CK Mapping

The detection is associated with **MITRE ATT&CK T1110 — Brute Force** as a hypothesis when repeated authentication failures are consistent with credential attack behavior. A sub-technique should only be assigned when the evidence supports it.
