# 02 — Network Topology

## Overview

To build a clean, resilient, and manageable enterprise campus, I organized the topology into three operational zones:
1. **Headquarters (HQ)**: Core multilayer switch, access switches, internal enterprise server, and departmental PC workstations.
2. **Branch Office**: A remote satellite office with its own router, access switch, and local PCs.
3. **Simulated Internet / External**: An upstream Internet Service Provider (ISP) router connected to the HQ perimeter edge router.

All port connections listed below have been verified inside Cisco Packet Tracer.

---

## Topology Diagram

```mermaid
graph TD
    subgraph ISP_Edge ["Simulated Internet / External"]
        ISP["ISP Router (Cisco 2811)<br/>Loopback0: 8.8.8.8/32"]
    end

    subgraph HQ_Perimeter ["HQ Edge & WAN Boundary"]
        HQR1["HQ-R1 Border Router (Cisco 2911)<br/>DCE Clock Provider | NAT Gateway | HQ DHCP | SSHv2"]
    end

    subgraph Branch_Site ["Branch Office"]
        BRR2["BR-R2 Branch Router (Cisco 2911)<br/>Branch RoaS | Branch DHCP | SSHv2"]
        BRSW1["BR-SW1 Access Switch (Cisco 2960)<br/>Port Security | SSHv2 (192.168.60.62)"]
        BRPC1["BR-PC1 (VLAN 60)<br/>DHCP: 192.168.60.2"]
        BRPC2["BR-PC2 (VLAN 70)<br/>DHCP: 192.168.70.2"]
    end

    subgraph HQ_LAN ["HQ Campus Network"]
        HQCORE["HQ-CORE Multilayer Switch (Cisco 3560-24PS)<br/>Inter-VLAN SVI Gateway | DHCP Relay | Guest ACL | Default Route"]
        HQACC1["HQ-ACC1 (Cisco 2960)<br/>HR & Engineering | Port Security | SSHv2 (192.168.10.62)"]
        HQACC2["HQ-ACC2 (Cisco 2960)<br/>IT & Guests | Port Security | SSHv2 (192.168.30.30)"]
        HQSERVER["HQ-SERVER (Server-PT)<br/>Static IP: 192.168.40.10"]

        HRPC1["HR-PC1 (VLAN 10)<br/>DHCP: 192.168.10.2"]
        HRPC2["HR-PC2 (VLAN 10)"]
        ENGPC1["ENG-PC1 (VLAN 20)<br/>DHCP: 192.168.20.2"]
        ENGPC2["ENG-PC2 (VLAN 20)"]
        ENGPC3["ENG-PC3 (VLAN 20)"]

        ITPC1["IT-PC1 (VLAN 30)<br/>DHCP: 192.168.30.2"]
        ITPC2["IT-PC2 (VLAN 30)"]
        GUESTPC1["GUEST-PC1 (VLAN 50)<br/>DHCP: 192.168.50.2"]
    end

    %% Connections
    ISP -- "Fa0/0 <=====> G0/1" --- HQR1
    HQR1 -- "S0/3/0 (DCE) <=====> S0/3/0" --- BRR2
    HQR1 -- "G0/0 <=====> Gi0/1 (Routed Transit: 10.255.0.0/30)" --- HQCORE

    HQCORE -- "Gi0/2 <=====> Gi0/1 (Trunk: 10, 20)" --- HQACC1
    HQCORE -- "Fa0/1 <=====> Gi0/1 (Trunk: 30, 50)" --- HQACC2
    HQCORE -- "Fa0/2 <=====> FastEthernet0" --- HQSERVER

    HQACC1 -- "Fa0/1" --- HRPC1
    HQACC1 -- "Fa0/2" --- HRPC2
    HQACC1 -- "Fa0/3" --- ENGPC1
    HQACC1 -- "Fa0/4" --- ENGPC2
    HQACC1 -- "Fa0/5" --- ENGPC3

    HQACC2 -- "Fa0/1" --- ITPC1
    HQACC2 -- "Fa0/2" --- ITPC2
    HQACC2 -- "Fa0/3" --- GUESTPC1

    BRR2 -- "G0/0 <=====> Gi0/1 (Trunk: 60, 70)" --- BRSW1
    BRSW1 -- "Fa0/1" --- BRPC1
    BRSW1 -- "Fa0/2" --- BRPC2
```

---

## Verified Interface Connection Matrix

Every single physical connection in the network was verified in Packet Tracer:

| Device A | Port A | Cable Type | Device B | Port B | Purpose / Verified Configuration |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **HQ-R1** | `G0/1` | Straight-Through | **ISP** | `Fa0/0` | Simulated Internet link (`203.0.113.0/30`). NAT Outside. |
| **HQ-R1** | `S0/3/0` | Serial (DCE) | **BR-R2** | `S0/3/0` | Point-to-point WAN leased line (`10.0.0.0/30`). Clock rate 64000 on HQ-R1. |
| **HQ-R1** | `G0/0` | Straight-Through | **HQ-CORE** | `Gi0/1` | HQ campus routed transit link (`10.255.0.0/30`). OSPF Area 0. |
| **HQ-CORE** | `Gi0/2` | Straight-Through | **HQ-ACC1** | `Gi0/1` | HQ 802.1Q trunk carrying VLANs 10 and 20. |
| **HQ-CORE** | `Fa0/1` | Straight-Through | **HQ-ACC2** | `Gi0/1` | HQ 802.1Q trunk carrying VLANs 30 and 50. |
| **HQ-CORE** | `Fa0/2` | Straight-Through | **HQ-SERVER**| `FastEthernet0` | Access port in VLAN 40 (Enterprise Server). |
| **HQ-ACC1** | `Fa0/1` | Straight-Through | **HR-PC1** | `FastEthernet0` | Access port in VLAN 10 (HR). Protected by Port Security. |
| **HQ-ACC1** | `Fa0/2` | Straight-Through | **HR-PC2** | `FastEthernet0` | Access port in VLAN 10 (HR). Protected by Port Security. |
| **HQ-ACC1** | `Fa0/3` | Straight-Through | **ENG-PC1** | `FastEthernet0` | Access port in VLAN 20 (ENG). Protected by Port Security. |
| **HQ-ACC1** | `Fa0/4` | Straight-Through | **ENG-PC2** | `FastEthernet0` | Access port in VLAN 20 (ENG). Protected by Port Security. |
| **HQ-ACC1** | `Fa0/5` | Straight-Through | **ENG-PC3** | `FastEthernet0` | Access port in VLAN 20 (ENG). Protected by Port Security. |
| **HQ-ACC2** | `Fa0/1` | Straight-Through | **IT-PC1** | `FastEthernet0` | Access port in VLAN 30 (IT). Protected by Port Security. |
| **HQ-ACC2** | `Fa0/2` | Straight-Through | **IT-PC2** | `FastEthernet0` | Access port in VLAN 30 (IT). Protected by Port Security. |
| **HQ-ACC2** | `Fa0/3` | Straight-Through | **GUEST-PC1**| `FastEthernet0` | Access port in VLAN 50 (Guest). Protected by Port Security. |
| **BR-R2** | `G0/0` | Straight-Through | **BR-SW1** | `Gi0/1` | Branch 802.1Q trunk (Router-on-a-Stick for VLANs 60 & 70). |
| **BR-SW1** | `Fa0/1` | Straight-Through | **BR-PC1** | `FastEthernet0` | Access port in VLAN 60 (BR-USERS). Protected by Port Security. |
| **BR-SW1** | `Fa0/2` | Straight-Through | **BR-PC2** | `FastEthernet0` | Access port in VLAN 70 (BR-ADMIN). Protected by Port Security. |

---

## Visual Verification Evidence

### Physical Cabling Layout in Packet Tracer
![Cisco Packet Tracer Physical Topology](../screenshots/01-physical-topology.png)

### Final Converged Topology with Green Link Status
![Final Converged Network Topology](../screenshots/15-final-topology.png)

---

Next: [03 — Device Inventory](03-device-inventory.md)
