# Splunk Universal Forwarder Setup

## Role

The Universal Forwarder runs on Windows and sends selected Windows Event Log channels to Splunk Enterprise on Kali.

## Destination

```text
192.168.123.1:9997
```

The destination is a lab-only host address on VMware VMnet1.

## Forwarding Configuration

`outputs.conf`:

```ini
[tcpout]
defaultGroup = kali_splunk

[tcpout:kali_splunk]
server = 192.168.123.1:9997
```

`inputs.conf`:

```ini
[WinEventLog://Security]
disabled = 0
index = windows
sourcetype = WinEventLog:Security

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
index = windows
sourcetype = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
```

## Service

The Windows service is `SplunkForwarder` and runs under:

```text
NT SERVICE\SplunkForwarder
```

## Configuration Validation

The Windows Universal Forwarder `btool` output was used to confirm the effective configuration for both forwarding and event inputs.

The service log was also used to confirm the transition from connection timeout to successful Splunk-to-Splunk connection.

Successful connection evidence included messages equivalent to:

```text
Connected to idx=192.168.123.1:9997
```

## Operational Lesson

A reachable TCP port is not enough. The receiver must be configured for the protocol the Universal Forwarder expects, and the Windows service account must have permission to read every event channel configured for collection.
