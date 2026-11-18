# 01 — Project Overview

## Project Background

I built this project as a final-year Computer Science student to demonstrate comprehensive, practical computer networking skills (at the CCNA level) for my engineering portfolio.

Instead of just following cookie-cutter lab steps, I designed, configured, and verified this enterprise network from scratch in Cisco Packet Tracer. The project represents a mid-sized company called **Apex Enterprise Solutions** that operates a Corporate Headquarters (HQ) and a regional Branch Office, connected by a leased serial WAN link, with secure edge routing to an upstream Internet Service Provider (ISP).

---

## What Problem This Network Solves

In a typical growing business, placing all devices into a single flat network causes multiple security and performance issues:
- **Broadcast Sprawl**: Without segmentation, broadcast storms and ARP chatter consume switch backplanes and PC processing power.
- **Lack of Security Boundaries**: An unsegmented network allows any regular employee or visiting guest to access sensitive payroll systems, engineering repositories, or administrative consoles.
- **Single Points of Failure & Inefficiency**: Routing all departmental traffic through a small edge router creates bandwidth bottlenecks.

To address these challenges, I implemented:
1. **Departmental VLAN Segmentation**: Segmented HQ into isolated VLANs for HR, Engineering, IT, Server Room, and Guests, and segmented the Branch into User and Admin segments.
2. **Layer-3 Core Switching**: Deployed a Cisco Catalyst 3560 multilayer switch (`HQ-CORE`) to handle inter-VLAN routing directly in hardware at wire speed via Switched Virtual Interfaces (SVIs).
3. **Dedicated WAN Connection**: Interconnected the HQ and Branch offices over a point-to-point serial leased line with synchronous V.35 DCE clocking.
4. **Dynamic Routing with OSPF**: Configured OSPFv2 (Process 1, Area 0) to automatically propagate routes between HQ and Branch without relying on static route clutter.
5. **Centralized & Local DHCP Services**: Configured centralized DHCP on `HQ-R1` with `HQ-CORE` relay helpers, and deployed survivable local DHCP on `BR-R2` for branch operations.
6. **Enterprise Server Infrastructure**: Configured `HQ-SERVER` with static IP addressing to provide internal DNS and corporate intranet web hosting.
7. **Perimeter NAT/PAT**: Configured and verified Port Address Translation (NAT Overload) on `HQ-R1`, translating RFC 1918 private subnets to the public outside interface.
8. **Zero-Trust Guest Isolation**: Built and verified the `GUEST_ONLY_INTERNET` extended ACL on `HQ-CORE` to strictly isolate guest devices from all internal company assets while allowing Internet reachability.
9. **Layer-2 Switchport Security**: Hardened access switchports across `HQ-ACC1`, `HQ-ACC2`, and `BR-SW1` with sticky MAC learning, a maximum limit of 2 MACs, and automated violation shutdown.
10. **Encrypted Remote Management**: Hardened the management plane across all routers and switches using SSHv2 with local administrative authentication, completely disabling cleartext Telnet.

---

## Completed Implementation Roadmap

Every phase of the project has been fully built, tested, and verified directly inside Cisco Packet Tracer:

```
[COMPLETED & VERIFIED]  Phase 1: Physical Topology & Interface Cabling
[COMPLETED & VERIFIED]  Phase 2: VLAN Creation & Switchport Configurations (Trunks & Access)
[COMPLETED & VERIFIED]  Phase 3: Layer-3 Switching on HQ-CORE & Branch Router-on-a-Stick
[COMPLETED & VERIFIED]  Phase 4: Serial WAN & OSPF Dynamic Routing (Area 0)
[COMPLETED & VERIFIED]  Phase 5: Local HQ Server Configuration (DNS & Web)
[COMPLETED & VERIFIED]  Phase 6: DHCP Allocation (HQ Relay via Core; Branch Local Pools)
[COMPLETED & VERIFIED]  Phase 7: Guest Isolation ACL (GUEST_ONLY_INTERNET)
[COMPLETED & VERIFIED]  Phase 8: Edge Routing & Default Route Troubleshooting Fix
[COMPLETED & VERIFIED]  Phase 9: NAT/PAT Overload & Live Translation Verification
[COMPLETED & VERIFIED]  Phase 10: Switchport Port Security Hardening (Sticky MACs)
[COMPLETED & VERIFIED]  Phase 11: Secure Remote Management (SSHv2 Login Verification)
[COMPLETED & VERIFIED]  Phase 12: Comprehensive End-to-End Verification & Documentation
```

---

## Verified Capabilities Summary

- **Layer-2 Switching**: 802.1Q trunks with explicit allowed VLAN lists, access ports assigned to departments, and Port Security active on all user-facing edge ports.
- **Inter-VLAN Routing**: Departmental workstations route seamlessly across `HQ-CORE` SVIs in hardware.
- **Enterprise Transit**: `HQ-CORE` and `HQ-R1` communicate over a dedicated `/30` routed transit link.
- **Branch Routing**: `BR-R2` routes between branch departments using 802.1Q Router-on-a-Stick subinterfaces.
- **WAN Reachability**: Serial WAN link (`10.0.0.0/30`) is up and active, exchanging dynamic routes via OSPF Area 0 with full neighbor adjacencies.
- **Automated IP Addressing**: All client PCs receive dynamic IP leases from their respective DHCP pools with proper gateway and DNS settings.
- **Intranet Services**: Local web browser testing confirms that `HQ-SERVER` (`192.168.40.10`) renders web pages and resolves internal queries.
- **Verified NAT/PAT**: Internal private traffic translates to the public IP `203.0.113.1` and establishes live sessions out to `8.8.8.8`.
- **Verified Guest Security**: Guests are blocked from internal subnets (`Destination host unreachable`) while being permitted to browse external sites.
- **Switchport Defense**: Unauthorized devices attaching to edge ports trigger immediate port shutdown.
- **Encrypted Administration**: Network administrators can securely manage switches and routers via SSHv2 from within the corporate network.

---

Next: [02 — Network Topology](02-topology.md)
