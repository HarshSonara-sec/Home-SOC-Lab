# Windows Failed Logon Investigation — Splunk

## Overview

This investigation demonstrates an analyst workflow for repeated Windows authentication failures using Windows Security telemetry collected by Splunk.

The activity was deliberately generated inside the isolated Home SOC Lab to validate telemetry, detection, search, and investigation. It is not presented as a confirmed compromise.

## Investigation Flow

```text
Windows Security Event 4625
        ↓
Splunk Universal Forwarder
        ↓
Splunk Enterprise
        ↓
windows index
        ↓
Detection / Search
        ↓
Analyst Triage
```

## Detection Search

```spl
index=windows sourcetype="WinEventLog:Security" EventCode=4625
```

The investigation was performed in Splunk Web using Search & Reporting.

## Observed Fields

The event data exposed useful authentication context, including:

- `Account_Name`
- `EventCode`
- `Failure_Reason`
- `Logon_Type`
- `Source_Network_Address`
- `Status`
- `Sub_Status`

The controlled simulation generated repeated failed authentication attempts against the lab account `socad` before a normal login.

## Analyst Interpretation

Repeated Event ID 4625 records can result from:

- User entering an incorrect password
- Stale credentials held by an application or service
- Misconfiguration
- Password spraying
- Brute-force activity
- Other authentication failures

A 4625 event alone does not prove malicious activity. The analyst must correlate frequency, source, targeted accounts, logon type, successful authentications, and other endpoint/network telemetry.

## Threshold Analysis

```spl
index=windows sourcetype="WinEventLog:Security" EventCode=4625
| bin _time span=5m
| stats count as failed_logins by _time, Account_Name, Source_Network_Address
| where failed_logins >= 3
| sort - _time
```

This query converts individual events into an analyst-oriented pattern by grouping failures into five-minute windows.

## Triage Questions

1. Which account was targeted?
2. What source address generated the failures?
3. Is the source expected for this endpoint?
4. Are multiple accounts being targeted?
5. What logon type was used?
6. Did a successful logon follow the failures?
7. Are there related Sysmon process/network events?
8. Is the account privileged or otherwise sensitive?
9. Does the pattern recur across endpoints?

## Response Recommendations

If the activity is unexplained:

- Validate the user and source system.
- Search for successful logons around the same activity.
- Investigate the source address and targeted accounts.
- Correlate endpoint telemetry.
- Escalate when the evidence indicates credential attack behavior.
- Consider credential protection or containment when compromise is suspected.

## MITRE ATT&CK

Potential mapping: **T1110 — Brute Force**.

The mapping should remain hypothesis-driven. Sub-techniques should not be assigned without evidence supporting the specific technique variant.

## Evidence

Portfolio evidence should include:

- Splunk search results for Event ID 4625
- Expanded event fields
- Threshold detection output
- Architecture/network diagram

Screenshots should be reviewed before publication to remove unnecessary usernames, host identifiers, or other personal information.
