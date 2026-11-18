# Cisco IOS Configurations

This folder is intended to store text backups of the Cisco IOS configurations (`show running-config`) exported from devices in Cisco Packet Tracer.

---

## Current Status

**No text configuration files exported yet.**

All routers and switches inside the Cisco Packet Tracer `.pkt` file are fully configured and verified. Text `.ios` backup files can be exported from Packet Tracer directly into this folder for portfolio code inspection.

---

## Planned Device Config Backups

The running configurations can be exported and saved here for:
- `HQ-CORE` (Catalyst 3560-24PS)
- `HQ-R1` (Cisco 2911 Router)
- `HQ-ACC1` (Catalyst 2960 Switch)
- `HQ-ACC2` (Catalyst 2960 Switch)
- `BR-R2` (Cisco 2911 Router)
- `BR-SW1` (Catalyst 2960 Switch)
- `ISP` (Cisco 2811 Router)

---

## How to Export Device Configs in Packet Tracer

When ready to export:
1. Open the CLI tab of the device in Packet Tracer.
2. Enter privileged EXEC mode:
   ```cisco
   enable
   ```
3. Turn off terminal paging and display the running configuration:
   ```cisco
   terminal length 0
   show running-config
   ```
4. Copy the output and save it as `<HOSTNAME>.ios` in this directory.
