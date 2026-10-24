# Windows 11 Endpoint Setup

## VM Profile

- Windows 11 Pro
- 2 vCPU
- 4 GB RAM
- 50 GB dynamically expanding virtual disk
- UEFI / Secure Boot / TPM 2.0 where supported
- VMware Tools installed
- Host-only networking for normal lab operation

## Endpoint Role

Windows is the telemetry-producing endpoint. It runs Sysmon and the Splunk Universal Forwarder.

## Collection Targets

The Universal Forwarder collects:

1. `Security` Windows Event Log
2. `Microsoft-Windows-Sysmon/Operational`

Both are assigned to the Splunk `windows` index.

## Network Validation

The endpoint must be able to reach the Splunk host on TCP 9997 before troubleshooting Splunk ingestion.

Example validation:

```powershell
Test-NetConnection 192.168.123.1 -Port 9997
```

A successful `TcpTestSucceeded : True` confirms basic TCP reachability; it does not by itself prove that the Splunk receiver is correctly configured.

## Operational Notes

- Keep the VM on host-only networking during normal SOC exercises.
- Switch to NAT only for controlled software downloads when necessary.
- Return the VM to host-only networking afterward.
- Do not place production credentials or personal data into the VM solely for lab testing.
