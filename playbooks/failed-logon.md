# Playbook: Repeated Windows Failed Logons

## Trigger

Repeated Windows Security Event ID 4625 events exceed the lab threshold.

## Initial Triage

1. Confirm the affected host.
2. Identify the targeted account.
3. Identify the source network address.
4. Review failure reason, status, sub-status, and logon type.
5. Determine whether the source and account relationship is expected.

## Correlation

Search for:

- Successful logons (4624)
- Additional 4625 events
- Multiple targeted accounts
- Sysmon process creation
- Sysmon network connections
- Sysmon DNS queries
- Other authentication or endpoint anomalies

## Decision Points

### Likely benign

Examples include a user entering the wrong password or an expected application repeatedly using stale credentials.

Document the reason and close or tune the detection if appropriate.

### Suspicious

Examples include repeated failures against multiple accounts, unexpected source systems, unusual logon types, or suspicious endpoint activity.

Escalate for deeper investigation.

### High concern

A burst of failures followed by a successful authentication, particularly against a privileged or sensitive account, should receive elevated priority and broader correlation.

## Response Considerations

Depending on evidence and organizational procedure:

- Validate account ownership.
- Preserve relevant evidence.
- Investigate source system.
- Protect or reset credentials if compromise is suspected.
- Contain affected systems when justified.
- Document the timeline and analytical reasoning.

## Closure Criteria

Close only when the activity has a defensible explanation or the investigation has been escalated with sufficient evidence.
