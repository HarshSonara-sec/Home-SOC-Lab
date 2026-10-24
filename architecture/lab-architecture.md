# Lab Architecture

## Objective

The lab provides an isolated environment for endpoint telemetry collection, SIEM ingestion, detection engineering, and SOC investigation on a resource-constrained workstation.

## Host Platform

- Intel 11th Gen i5-11320H, 4 cores / 8 threads
- 7.5 GiB RAM
- Kali GNU/Linux Rolling
- VMware Workstation
- Hardware virtualization enabled

The design intentionally avoids unnecessary always-on workloads.

## Virtual Machines

### Kali / SIEM Host

Kali hosts Splunk Enterprise and provides the primary SIEM/search environment.

### Windows 11 Endpoint

Windows 11 generates Windows Security and Sysmon telemetry. Splunk Universal Forwarder sends selected event channels to Splunk Enterprise.

### Metasploitable2

Metasploitable2 is retained for future controlled exercises. It is kept on the isolated lab network and is not intended to be bridged onto the physical LAN.

## Network Segmentation

| Network | Range | Purpose |
|---|---|---|
| VMware VMnet1 | 192.168.123.0/24 | Host-only SOC lab network |
| VMware VMnet8 | 192.168.89.0/24 | NAT network used only when controlled Internet access is required |
| VMware VMnet0 | Physical/bridged | Not used for the vulnerable lab systems |

The addresses above are RFC1918 lab addresses and have no intended public routing role.

## Telemetry Path

```text
Windows 11
  |
  +-- Windows Security Event Log
  |
  +-- Sysmon Operational Log
  |
  v
Splunk Universal Forwarder
  |
  | TCP 9997 / Splunk-to-Splunk protocol
  v
Splunk Enterprise on Kali
  |
  v
windows index
  |
  +--> Splunk Search & Reporting
  +--> Detection logic
  +--> Investigation
  +--> Reporting
```

## Design Decisions

1. **Host-only by default:** Windows and vulnerable systems remain isolated from the physical network.
2. **NAT only when needed:** Internet access is enabled temporarily for tasks such as downloading software, then the endpoint is returned to host-only networking.
3. **Resource-aware telemetry:** Sysmon collection is scoped to useful process, network, DNS, file, and persistence-related events.
4. **GUI-first SOC workflow:** Splunk Web is preferred for search, triage, detections, and investigation; terminal work is reserved for infrastructure administration and troubleshooting.
5. **Evidence-driven documentation:** Every detection should be traceable from raw telemetry to analyst interpretation.
