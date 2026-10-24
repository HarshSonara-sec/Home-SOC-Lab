# Troubleshooting: Sysmon Forwarding Access Denied

## Symptom

Security events were arriving, but the Universal Forwarder could not subscribe to the Sysmon event channel.

The forwarder log reported:

```text
WinEventLogChannel::subscribeToEvtChannel: Could not subscribe to Windows Event Log channel
'Microsoft-Windows-Sysmon/Operational'

Init failed ... errorCode=5
```

Windows error code 5 means **Access Denied**.

## Investigation

The Sysmon channel security descriptor included the Event Log Readers group:

```text
channelAccess: O:BAG:SYD:(A;;0xf0007;;;SY)(A;;0x7;;;BA)(A;;0x1;;;BO)(A;;0x1;;;SO)(A;;0x1;;;S-1-5-32-573)
```

The SID `S-1-5-32-573` corresponds to **Event Log Readers**.

The Universal Forwarder service account was identified as:

```text
NT SERVICE\SplunkForwarder
```

## Resolution

Add the service account to Event Log Readers:

```powershell
Add-LocalGroupMember -Group "Event Log Readers" -Member "NT SERVICE\SplunkForwarder"
```

Verify membership:

```powershell
Get-LocalGroupMember -Group "Event Log Readers"
```

Restart the forwarder:

```powershell
Restart-Service SplunkForwarder
```

## Verification

After the permission change and service restart, Sysmon events appeared in Splunk. This validated that the issue was event-channel access rather than network connectivity or the Splunk receiver.

## Lesson

Troubleshoot telemetry pipelines layer by layer:

1. Network reachability
2. Receiver/protocol configuration
3. Forwarder effective configuration
4. Service identity
5. Event-channel permissions
6. Index/search validation

This prevents unrelated warnings from being mistaken for the root cause.
