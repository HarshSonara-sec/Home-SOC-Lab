# VMware Workstation Setup

## Virtualization Platform

VMware Workstation is used to run the isolated Windows and Metasploitable2 guests.

## Network Configuration

### VMnet1 — Host-only

- Network: `192.168.123.0/24`
- DHCP: enabled
- Kali VMnet1 address: `192.168.123.1`
- Primary SOC lab network

### VMnet8 — NAT

- Network: `192.168.89.0/24`
- DHCP: enabled
- Used only when a guest temporarily requires controlled Internet access

### VMnet0 — Bridged

Not used for the vulnerable lab systems.

## Windows Graphics Issue

When the Windows VM was switched from NAT to host-only networking, VMware graphics initialization failed. The issue was resolved by disabling VMware 3D acceleration in the VMX configuration:

```text
mks.enable3d = "FALSE"
```

The VM then started normally.

## Network Safety

The intended steady state is:

```text
Windows 11  ---> VMnet1 ---> Kali/Splunk
Metasploitable2 ---> VMnet1
```

Do not bridge Metasploitable2 or other intentionally vulnerable systems onto the physical LAN.
