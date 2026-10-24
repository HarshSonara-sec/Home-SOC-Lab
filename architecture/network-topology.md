# Network Topology

## Host-Only SOC Network

```text
                 VMware VMnet1
                 192.168.123.0/24
                         |
        +----------------+----------------+
        |                                 |
   Kali / Splunk                     Windows 11
  192.168.123.1                    192.168.123.x
        |                                 |
        |<------ TCP 9997 --------------->|
        |                                 |
   Splunk Enterprise              Universal Forwarder
```

The Windows endpoint forwards telemetry to the Kali/Splunk host over the host-only network.

## NAT Network

VMnet8 uses `192.168.89.0/24` for controlled Internet access. The Windows endpoint was temporarily placed on this network when software had to be downloaded, then returned to VMnet1.

## Isolation Requirements

- Do not bridge intentionally vulnerable systems to the physical LAN.
- Keep Metasploitable2 on isolated lab networking.
- Use NAT only when required.
- Return Windows to host-only networking after external downloads.
- Validate the intended adapter before running any security simulation.
