# Kali Host Setup

## Host Role

Kali serves as the virtualization and SIEM host for the lab. Splunk Enterprise runs locally on Kali under the dedicated `splunk` service account.

## Resource Profile

The host has approximately 7.5 GiB RAM and a four-core/eight-thread Intel CPU. Because the lab runs on a single laptop, resource use is deliberately controlled.

## Operational Checks

Before starting the lab, review available memory and swap:

```bash
free -h
```

After shutting down VMs, the same command can be used to verify that memory is released.

## Safety Boundaries

- Do not delete or modify Timeshift snapshots as part of normal lab maintenance.
- Do not alter Windows NTFS partitions for lab cleanup.
- Keep vulnerable VMs isolated.
- Avoid running unnecessary VMs concurrently.
- Power off VMs when the exercise is complete.
