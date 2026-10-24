# Windows Failed Login Investigation Report

## Executive Summary

A controlled authentication-failure simulation was performed against a Windows 11 endpoint in the isolated Home SOC Lab. Windows Security Event ID 4625 records were successfully collected by the Splunk Universal Forwarder, indexed by Splunk Enterprise, and investigated through Splunk Search & Reporting.

The exercise validated the complete telemetry-to-investigation pipeline.

## Scope

**In scope:**

- Windows Security authentication telemetry
- Splunk Universal Forwarder
- Splunk Enterprise
- Event ID 4625
- Detection thresholding
- SOC triage methodology

**Out of scope:**

- Real-world compromise
- Production credentials
- External targets
- Attribution of malicious intent

## Detection

```spl
index=windows sourcetype="WinEventLog:Security" EventCode=4625
```

## Observations

The controlled simulation produced repeated failed authentication events for the lab account `socad`. Event data exposed fields including account name, logon type, failure reason, source network address, status, and sub-status.

## Pattern Detection

```spl
index=windows sourcetype="WinEventLog:Security" EventCode=4625
| bin _time span=5m
| stats count as failed_logins by _time, Account_Name, Source_Network_Address
| where failed_logins >= 3
| sort - _time
```

## Assessment

The activity demonstrates the telemetry pattern that a SOC analyst could use to identify repeated authentication failures. The lab evidence alone does not establish malicious intent because the events were deliberately generated as a controlled test.

## Recommended Analyst Actions

- Validate the account owner and expected activity.
- Check source address and endpoint context.
- Search for successful logons around the same window.
- Determine whether multiple accounts were targeted.
- Review logon type and failure codes.
- Correlate endpoint process/network telemetry.
- Escalate when evidence supports credential attack behavior.

## ATT&CK

Potential mapping: T1110 — Brute Force.

Use sub-techniques only when the observed evidence supports the distinction.

## Outcome

The lab demonstrated:

`Generate → Collect → Forward → Index → Search → Detect → Investigate → Report`

This report is intended as a portfolio example of evidence-based SOC analysis rather than a claim of real-world incident response.
