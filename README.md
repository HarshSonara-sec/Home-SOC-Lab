# Home SOC Lab

A practical, resource-conscious Security Operations Center (SOC) lab built on a single laptop to develop and demonstrate hands-on skills in Windows telemetry, endpoint visibility, SIEM operations, detection engineering, alert triage, investigation, troubleshooting, and security documentation.

> **Portfolio note:** This repository documents an established practical SOC lab and the engineering decisions behind it. It is intentionally focused on reproducibility, troubleshooting, evidence-driven analysis, and operational understanding rather than a simple tool-installation walkthrough.

## Lab at a Glance

The lab uses an isolated virtual network to generate and collect security telemetry without exposing intentionally vulnerable systems to the local network.

```text
                         Host Laptop
                    Kali GNU/Linux Rolling
                    VMware Workstation
                           |
             +-------------+-------------+
             |                           |
       Host-only VMnet1              NAT VMnet8
       192.168.123.0/24             192.168.89.0/24
             |                           |
       +-----+------+              Internet access
       |            |
 Windows 11     Kali/Splunk
   Endpoint       Enterprise
       |            |
 Sysmon +          |
 Security Log      |
       |            |
 Splunk UF --------+---- TCP 9997
       |
       +-------------------->
                    Splunk Enterprise
                    index=windows
```

## Core Components

| Component | Role |
|---|---|
| Kali Linux | SOC/SIEM host and security tooling platform |
| VMware Workstation | Virtualization layer |
| Windows 11 | Endpoint telemetry source |
| Sysmon | Detailed Windows endpoint telemetry |
| Splunk Universal Forwarder | Endpoint log collection and forwarding |
| Splunk Enterprise | SIEM, search, detection and investigation platform |
| Metasploitable2 | Isolated vulnerable system reserved for future controlled exercises |

## Data Flow

```text
Windows Security Events
        +
      Sysmon
        |
        v
Splunk Universal Forwarder
        |
        | Splunk-to-Splunk / TCP 9997
        v
Splunk Enterprise on Kali
        |
        v
windows index
        |
        +--> Search
        +--> Detection
        +--> Triage
        +--> Investigation
        +--> Reporting
```

## Telemetry Coverage

### Windows Security

The lab collects Windows Security Event Log data, including authentication telemetry such as Event ID 4625 (failed logon).

### Sysmon

The Sysmon configuration used in the lab provides visibility into:

- Process creation
- Network connections
- DNS queries
- Selected file creation activity
- Selected registry activity

Key Sysmon events used for analysis include:

| Event ID | Purpose |
|---:|---|
| 1 | Process creation |
| 3 | Network connection |
| 11 | File creation |
| 12–14 | Registry activity |
| 22 | DNS query |

## Detection & Investigation Workflow

The lab follows an analyst-oriented workflow:

```text
ATTACK / SIMULATION
        ↓
TELEMETRY
        ↓
SIEM INGESTION
        ↓
DETECTION
        ↓
ALERT / TRIAGE
        ↓
INVESTIGATION
        ↓
IOC EXTRACTION
        ↓
MITRE ATT&CK MAPPING
        ↓
RESPONSE RECOMMENDATION
        ↓
INCIDENT REPORT
```

## Investigation: Windows Failed Logons

The first documented investigation in this repository uses Windows Security Event ID 4625 to examine failed authentication activity.

The activity was generated as a controlled lab simulation to validate the complete telemetry and investigation pipeline. It is not represented as a real-world compromise.

The investigation covers:

- Event ID 4625
- Account and source context
- Logon type
- Failure information
- Threshold-based detection logic
- SOC triage considerations
- MITRE ATT&CK T1110 mapping
- Response recommendations

See [`investigations/windows-failed-logins.md`](investigations/windows-failed-logins.md).

## Engineering & Troubleshooting

The lab documentation preserves important implementation problems and their resolutions rather than presenting only a perfect end state.

Examples include:

- Splunk receiver configuration mismatch
- Universal Forwarder cooked-connection timeouts
- Correct Splunk-to-Splunk listener configuration on TCP 9997
- Sysmon event-channel access failure (`errorCode=5`)
- Windows Event Log Readers permission correction for the `SplunkForwarder` service account
- Verification of successful telemetry ingestion

See [`troubleshooting/`](troubleshooting/).

## Repository Structure

```text
Home-SOC-Lab/
├── README.md
├── architecture/
├── setup/
├── configurations/
├── detections/
├── investigations/
├── troubleshooting/
├── commands/
├── screenshots/
├── mitre/
├── sources/
├── playbooks/
└── reports/
```

## Documentation Principles

This repository is designed to be useful to both a reviewer and another learner reproducing the lab.

Documentation therefore emphasizes:

- Why a component was selected
- How components communicate
- What telemetry is produced
- How configuration decisions affect collection
- What failed during implementation
- How failures were diagnosed
- How detections are constructed
- How an analyst validates an alert
- What evidence supports an investigation
- What should and should not be inferred from an event

## Resource-Conscious Design

The environment was deliberately designed around constrained hardware. Virtual machines are isolated and powered off when not required. Telemetry collection is intentionally scoped rather than enabling every possible Windows event source.

This keeps the lab practical while still providing meaningful endpoint and SIEM visibility.

## Security & Privacy

This repository must never contain:

- Passwords
- API keys
- Tokens
- Private keys
- VM disk images
- Personal account identifiers
- Unredacted sensitive screenshots
- Production credentials

Configuration examples should use placeholders where secrets would otherwise appear.

## Portfolio Evidence

Screenshots and diagrams will be added under `screenshots/` and `architecture/` after being reviewed for unnecessary identifying information.

The repository intentionally avoids relying on dates or timestamps in its narrative so that the project remains a durable technical portfolio artifact rather than a dated activity log.

## Sources

Authoritative vendor and security-framework references used while building and documenting the lab are collected under [`sources/references.md`](sources/references.md).

## Status

The core Windows telemetry → Splunk pipeline and the failed-logon investigation workflow have been validated. The repository is now being organized into reproducible setup documentation, detection content, investigation reports, troubleshooting records, command references, evidence, and security-framework mappings.
