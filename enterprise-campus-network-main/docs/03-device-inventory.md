# 03 — Device Inventory & Hardware Details

## Overview

Here is the inventory of all the network devices deployed in the Cisco Packet Tracer workspace. All chassis, modules, servers, and PC endpoints have been standardized with consistent hostnames, IP assignments, and administrative security roles.

---

## Routers & Switches

| Device Hostname | Model | Device Type | Modules / Interface Slots | Management IP / SVI | Role in the Network |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **HQ-R1** | Cisco 2911 | Router | Built-in: `G0/0`, `G0/1`, `G0/2`<br/>Slot 0: `HWIC-2T` (`S0/3/0`, `S0/3/1`) | `10.255.0.2` (Transit)<br/>`10.0.0.1` (WAN) | HQ perimeter router. NAT/PAT gateway, WAN OSPF edge, HQ DHCP server, SSHv2 enabled. |
| **BR-R2** | Cisco 2911 | Router | Built-in: `G0/0`, `G0/1`, `G0/2`<br/>Slot 0: `HWIC-2T` (`S0/3/0`, `S0/3/1`) | `10.0.0.2` (WAN)<br/>`192.168.60.1` / `70.1` | Branch router. Router-on-a-Stick gateway, WAN OSPF edge, branch DHCP server, SSHv2 enabled. |
| **ISP** | Cisco 2811 | Router | Built-in: `Fa0/0`, `Fa0/1`<br/>Logical: `Loopback0` (`8.8.8.8/32`) | `203.0.113.2`<br/>Loopback: `8.8.8.8` | Simulates the external Internet Service Provider and public Internet DNS target. |
| **HQ-CORE** | Cisco 3560-24PS | Layer-3 Switch | 24 FastEthernet ports (`Fa0/1-24`)<br/>2 GigabitEthernet ports (`Gi0/1-2`) | `10.255.0.1` (Transit)<br/>SVIs: `10.1`, `20.1`, `30.1`, `40.1`, `50.1` | HQ core switch. Multilayer inter-VLAN routing, DHCP relay to HQ-R1, static default route, Guest ACL. |
| **HQ-ACC1** | Cisco 2960-24TT | Layer-2 Switch | 24 FastEthernet ports (`Fa0/1-24`)<br/>2 GigabitEthernet ports (`Gi0/1-2`) | `192.168.10.62` (SVI Vlan10) | Access switch for HR and Engineering PCs. Port Security active on Fa0/1-5. SSHv2 enabled. |
| **HQ-ACC2** | Cisco 2960-24TT | Layer-2 Switch | 24 FastEthernet ports (`Fa0/1-24`)<br/>2 GigabitEthernet ports (`Gi0/1-2`) | `192.168.30.30` (SVI Vlan30) | Access switch for IT Support and Guest PCs. Port Security active on Fa0/1-3. SSHv2 enabled. |
| **BR-SW1** | Cisco 2960-24TT | Layer-2 Switch | 24 FastEthernet ports (`Fa0/1-24`)<br/>2 GigabitEthernet ports (`Gi0/1-2`) | `192.168.60.62` (SVI Vlan60) | Access switch for Branch users and admin PCs. Port Security active on Fa0/1-2. SSHv2 enabled. |

---

## Serial Modules (HWIC-2T)

The Cisco 2911 routers do not have serial ports out of the box. To support the leased-line serial WAN connection between HQ and Branch, I installed modular cards:
- **Card**: `HWIC-2T` (2-Port Serial High-Speed WAN Interface Card)
- **Slot**: Installed into Slot 0 (subslot 3)
- **Active Port**: `Serial0/3/0` on both `HQ-R1` and `BR-R2`

---

## Dedicated Infrastructure Servers

| Hostname | Type | Connected Switch Port | VLAN | Configured IP | Active Services |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **HQ-SERVER** | Server-PT | `HQ-CORE Fa0/2` | 40 (SERVER) | `192.168.40.10/28` (Gateway: `192.168.40.1`) | HTTP (Port 80) and DNS (Port 53) enabled and verified. |

---

## Client Workstations

| Hostname | Location | Switch Connection | Switch Port | Assigned VLAN | Verified IP Address | Assignment Method | Port Security Status |
| :--- | :--- | :--- | :--- | :---: | :--- | :--- | :---: |
| **HR-PC1** | HQ | `HQ-ACC1` | `Fa0/1` | 10 (HR) | `192.168.10.2` | DHCP (HQ-R1) | **SecureSticky (Active)** |
| **HR-PC2** | HQ | `HQ-ACC1` | `Fa0/2` | 10 (HR) | `192.168.10.3` | DHCP (HQ-R1) | **SecureSticky (Active)** |
| **ENG-PC1** | HQ | `HQ-ACC1` | `Fa0/3` | 20 (ENG) | `192.168.20.2` | DHCP (HQ-R1) | **SecureSticky (Active)** |
| **ENG-PC2** | HQ | `HQ-ACC1` | `Fa0/4` | 20 (ENG) | `192.168.20.3` | DHCP (HQ-R1) | **SecureSticky (Active)** |
| **ENG-PC3** | HQ | `HQ-ACC1` | `Fa0/5` | 20 (ENG) | `192.168.20.4` | DHCP (HQ-R1) | **SecureSticky (Active)** |
| **IT-PC1** | HQ | `HQ-ACC2` | `Fa0/1` | 30 (IT) | `192.168.30.2` | DHCP (HQ-R1) | **SecureSticky (Active)** |
| **IT-PC2** | HQ | `HQ-ACC2` | `Fa0/2` | 30 (IT) | `192.168.30.3` | DHCP (HQ-R1) | **SecureSticky (Active)** |
| **GUEST-PC1**| HQ | `HQ-ACC2` | `Fa0/3` | 50 (GUEST) | `192.168.50.2` | DHCP (HQ-R1) | **SecureSticky (Active)** |
| **BR-PC1** | Branch | `BR-SW1` | `Fa0/1` | 60 (BR-USERS) | `192.168.60.2` | DHCP (BR-R2) | **SecureSticky (Active)** |
| **BR-PC2** | Branch | `BR-SW1` | `Fa0/2` | 70 (BR-ADMIN) | `192.168.70.2` | DHCP (BR-R2) | **SecureSticky (Active)** |

---

Next: [04 — IP Addressing & Subnet Plan](04-ip-addressing.md)
