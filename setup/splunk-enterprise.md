# Splunk Enterprise Setup

## Role

Splunk Enterprise runs on Kali and provides the SIEM/search layer for Windows Security and Sysmon telemetry.

## Installation

The Linux 64-bit Splunk Enterprise package was installed under `/opt/splunk`.

After package installation, ownership was assigned to a dedicated `splunk` service user instead of operating the service as root.

```bash
sudo chown -R splunk:splunk /opt/splunk
sudo -u splunk /opt/splunk/bin/splunk start --accept-license
sudo -u splunk /opt/splunk/bin/splunk status
```

## Index

The lab uses a dedicated index named `windows`.

```bash
sudo -u splunk /opt/splunk/bin/splunk add index windows
```

## Receiving Port

The Universal Forwarder uses Splunk-to-Splunk forwarding to TCP 9997.

The correct listener is created with:

```bash
sudo -u splunk /opt/splunk/bin/splunk enable listen 9997 -auth 'admin:<SPLUNK_ADMIN_PASSWORD>'
```

After enabling the listener, restart Splunk and validate the port:

```bash
sudo -u splunk /opt/splunk/bin/splunk restart
sudo -u splunk /opt/splunk/bin/splunk list tcp
ss -lntp | grep 9997
```

## Important Configuration Lesson

The initial attempt used:

```bash
sudo -u splunk /opt/splunk/bin/splunk add tcp 9997 -sourcetype WinEventLog
```

That created a generic `[tcp://9997]` input rather than the required Splunk-to-Splunk receiver. The Universal Forwarder subsequently reported cooked-connection timeouts.

The incorrect input was removed and the proper `enable listen 9997` configuration was enabled. After the change, the Universal Forwarder connected successfully.

This is preserved as a troubleshooting case rather than hidden from the project history.

## Search Validation

Once ingestion is working, Splunk can validate source coverage with:

```spl
index=windows | stats count by sourcetype
```

The lab successfully received Windows Security data and, after the Event Log Readers permission fix, Sysmon data as well.

## GUI Workflow

For normal SOC operations, use **Splunk Web → Search & Reporting** for:

- Event exploration
- Field inspection
- Detection searches
- Threshold analysis
- Investigation pivots
- Saved searches and dashboards

Terminal commands are reserved for service administration, receiver validation, and troubleshooting.
