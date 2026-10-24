# Troubleshooting: Splunk Receiver Configuration

## Symptom

The Windows Universal Forwarder was configured for `192.168.123.1:9997`, but the forwarder log initially reported a cooked-connection timeout.

Example symptom:

```text
Cooked connection to ip=192.168.123.1:9997 timed out
```

## Root Cause

The initial Splunk configuration used a generic TCP input:

```bash
sudo -u splunk /opt/splunk/bin/splunk add tcp 9997 -sourcetype WinEventLog
```

This created a `[tcp://9997]` input instead of the Splunk-to-Splunk receiver expected by the Universal Forwarder.

## Resolution

Remove the incorrect input:

```bash
sudo -u splunk /opt/splunk/bin/splunk remove tcp 9997
```

Enable the proper Splunk-to-Splunk listener:

```bash
sudo -u splunk /opt/splunk/bin/splunk enable listen 9997 -auth 'admin:<SPLUNK_ADMIN_PASSWORD>'
```

Restart Splunk:

```bash
sudo -u splunk /opt/splunk/bin/splunk restart
```

Validate:

```bash
sudo -u splunk /opt/splunk/bin/splunk list tcp
ss -lntp | grep 9997
```

## Verification

The Universal Forwarder subsequently logged successful connections to the Splunk host on port 9997. Windows Security events then appeared in the `windows` index.

## Lesson

Always distinguish between a generic TCP input and a Splunk-to-Splunk receiving port. Network reachability alone does not establish protocol compatibility.
