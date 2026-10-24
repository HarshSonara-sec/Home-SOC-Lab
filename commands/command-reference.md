# Home SOC Lab Command Reference

This file records the important administrative, validation, and investigation commands used during the lab build. Secrets are intentionally replaced with placeholders.

## Kali / Splunk

### Check host resources

```bash
free -h
```

Shows RAM and swap utilization before or after running VMs.

### Check VMware processes

```bash
pgrep -a vmware
```

Used to confirm whether VMware components remained after VMs were powered off. Background VMware services are not automatically errors.

### Install Splunk package

```bash
sudo dpkg -i <SPLUNK_DEB_PACKAGE>
```

Installs the downloaded Splunk Enterprise Debian package.

### Verify package installation

```bash
dpkg --status splunk
```

Confirms that the Splunk package is installed.

### Assign service ownership

```bash
sudo chown -R splunk:splunk /opt/splunk
```

Allows Splunk to run under its dedicated service account.

### Start / check Splunk

```bash
sudo -u splunk /opt/splunk/bin/splunk start --accept-license
sudo -u splunk /opt/splunk/bin/splunk status
```

### Create Windows index

```bash
sudo -u splunk /opt/splunk/bin/splunk add index windows
```

Creates the dedicated `windows` index.

### Initial incorrect receiver attempt

```bash
sudo -u splunk /opt/splunk/bin/splunk add tcp 9997 -sourcetype WinEventLog
```

This was intentionally retained as a troubleshooting record. It produced a generic TCP input rather than the correct Splunk-to-Splunk listener.

### Remove incorrect receiver

```bash
sudo -u splunk /opt/splunk/bin/splunk remove tcp 9997
```

### Enable Splunk-to-Splunk receiver

```bash
sudo -u splunk /opt/splunk/bin/splunk enable listen 9997 -auth 'admin:<SPLUNK_ADMIN_PASSWORD>'
```

Never commit a real password in this command.

### Restart Splunk

```bash
sudo -u splunk /opt/splunk/bin/splunk restart
```

### List listening Splunk data ports

```bash
sudo -u splunk /opt/splunk/bin/splunk list tcp
```

### Verify TCP listener

```bash
ss -lntp | grep 9997
```

Confirms that `splunkd` is listening on TCP 9997.

### Validate sourcetypes

```bash
sudo -u splunk /opt/splunk/bin/splunk search 'index=windows | stats count by sourcetype'
```

### Validate Security 4625 events

```bash
sudo -u splunk /opt/splunk/bin/splunk search 'index=windows sourcetype="WinEventLog:Security" EventCode=4625'
```

### Failed-logon aggregation

```bash
sudo -u splunk /opt/splunk/bin/splunk search 'index=windows sourcetype="WinEventLog:Security" EventCode=4625 | stats count by Account_Name, Source_Network_Address | sort - count'
```

## Windows / PowerShell

### Test Splunk receiver reachability

```powershell
Test-NetConnection 192.168.123.1 -Port 9997
```

### Inspect Sysmon events locally

```powershell
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" -MaxEvents 5 | Select-Object TimeCreated, Id, ProviderName
```

### Inspect Sysmon channel permissions

```powershell
wevtutil gl "Microsoft-Windows-Sysmon/Operational"
```

### Identify Splunk Forwarder service account

```powershell
Get-CimInstance Win32_Service -Filter "Name='SplunkForwarder'" | Select-Object Name, StartName, State
```

### Add forwarder service to Event Log Readers

```powershell
Add-LocalGroupMember -Group "Event Log Readers" -Member "NT SERVICE\SplunkForwarder"
```

### Verify group membership

```powershell
Get-LocalGroupMember -Group "Event Log Readers"
```

### Restart forwarder

```powershell
Restart-Service SplunkForwarder
```

### Apply Sysmon configuration

```powershell
sysmon -c C:\Windows\System32\soc-sysmon.xml
```

### Inspect Universal Forwarder logs

```powershell
Get-Content "C:\Program Files\SplunkUniversalForwarder\var\log\splunk\splunkd.log" -Tail 300 | Select-String -Pattern "Sysmon|WinEventLog|EventLog|error|warn"
```

Use this to correlate connection and event-channel errors.

## Investigation Simulation

A controlled failed-login simulation was performed by locking the Windows workstation, entering an incorrect password several times for the lab account, and then completing a normal login.

No offensive tooling was required for this validation.

## Notes

- Commands that contain passwords use placeholders only.
- Local paths are included only where they are useful for reproducibility.
- The command list intentionally focuses on the commands that materially contributed to configuration, validation, troubleshooting, and investigation.
