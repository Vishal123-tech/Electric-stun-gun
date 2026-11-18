# 04 — IP Addressing & VLSM Subnet Plan

## Why VLSM?

Instead of giving every department a whole `/24` subnet (which gives 254 usable addresses and wastes a lot of IP space), I used **Variable Length Subnet Masking (VLSM)**. 

VLSM lets me tailor the size of each subnet to what each department or link actually needs:
- Engineering needs more addresses because it is a larger technical team with multiple test machines.
- IT needs fewer addresses.
- The server room only needs a small subnet for dedicated infrastructure.
- Point-to-point links between routers only need 2 usable addresses, so a `/30` is perfect.
- Dedicated switch management SVIs are placed cleanly at the top of their respective departmental subnets.

---

## Complete IP Addressing Table

| Subnet / Purpose | VLAN ID | Network Address | Prefix | Subnet Mask | Usable Range | Broadcast Address | Default Gateway | Key Static / SVI Assignments |
| :--- | :---: | :--- | :---: | :--- | :--- | :--- | :--- | :--- |
| **HR Department** | 10 | `192.168.10.0` | `/26` | `255.255.255.192` | `192.168.10.1 - .62` | `192.168.10.63` | `192.168.10.1` | `HQ-ACC1` SVI: `192.168.10.62` |
| **Engineering** | 20 | `192.168.20.0` | `/25` | `255.255.255.128` | `192.168.20.1 - .126` | `192.168.20.127` | `192.168.20.1` | SVI on `HQ-CORE` |
| **IT Support** | 30 | `192.168.30.0` | `/27` | `255.255.255.224` | `192.168.30.1 - .30` | `192.168.30.31` | `192.168.30.1` | `HQ-ACC2` SVI: `192.168.30.30` |
| **Server Room** | 40 | `192.168.40.0` | `/28` | `255.255.255.240` | `192.168.40.1 - .14` | `192.168.40.15` | `192.168.40.1` | `HQ-SERVER`: `192.168.40.10` |
| **Guest Network** | 50 | `192.168.50.0` | `/24` | `255.255.255.0` | `192.168.50.1 - .254` | `192.168.50.255` | `192.168.50.1` | SVI on `HQ-CORE` |
| **Branch Users** | 60 | `192.168.60.0` | `/26` | `255.255.255.192` | `192.168.60.1 - .62` | `192.168.60.63` | `192.168.60.1` | `BR-SW1` SVI: `192.168.60.62` |
| **Branch Admin** | 70 | `192.168.70.0` | `/26` | `255.255.255.192` | `192.168.70.1 - .62` | `192.168.70.63` | `192.168.70.1` | SVI on `BR-R2` (RoaS) |
| **HQ Transit Link** | — | `10.255.0.0` | `/30` | `255.255.255.252` | `10.255.0.1 - .2` | `10.255.0.3` | N/A | `HQ-CORE`: `.1`, `HQ-R1`: `.2` |
| **Serial WAN Link** | — | `10.0.0.0` | `/30` | `255.255.255.252` | `10.0.0.1 - .2` | `10.0.0.3` | N/A | `HQ-R1`: `.1`, `BR-R2`: `.2` |
| **ISP Link** | — | `203.0.113.0` | `/30` | `255.255.255.252` | `203.0.113.1 - .2` | `203.0.113.3` | `203.0.113.2` | `HQ-R1`: `.1`, `ISP`: `.2` |
| **ISP Loopback** | — | `8.8.8.8` | `/32` | `255.255.255.255` | `8.8.8.8` | `8.8.8.8` | N/A | Simulated Public DNS |

---

## Detailed Department Breakdown

### 1. VLAN 10: Human Resources (`192.168.10.0/26`)
- **Mask**: `255.255.255.192` (6 host bits: $2^6 - 2 = 62$ usable addresses)
- **Gateway**: `192.168.10.1` on `HQ-CORE` SVI `interface Vlan10`
- **Management SVI**: `HQ-ACC1` is configured with `192.168.10.62` for SSHv2 access.
- **Verified Host**: `HR-PC1` dynamically receives `192.168.10.2` via DHCP from `HQ-R1`.

### 2. VLAN 20: Engineering (`192.168.20.0/25`)
- **Mask**: `255.255.255.128` (7 host bits: $2^7 - 2 = 126$ usable addresses)
- **Gateway**: `192.168.20.1` on `HQ-CORE` SVI `interface Vlan20`
- **Verified Host**: `ENG-PC1` dynamically receives `192.168.20.2` via DHCP from `HQ-R1`.

### 3. VLAN 30: IT Support (`192.168.30.0/27`)
- **Mask**: `255.255.255.224` (5 host bits: $2^5 - 2 = 30$ usable addresses)
- **Gateway**: `192.168.30.1` on `HQ-CORE` SVI `interface Vlan30`
- **Management SVI**: `HQ-ACC2` is configured with `192.168.30.30` for SSHv2 access.
- **Verified Host**: `IT-PC1` dynamically receives `192.168.30.2` via DHCP from `HQ-R1`.

### 4. VLAN 40: Server Room (`192.168.40.0/28`)
- **Mask**: `255.255.255.240` (4 host bits: $2^4 - 2 = 14$ usable addresses)
- **Gateway**: `192.168.40.1` on `HQ-CORE` SVI `interface Vlan40`
- **Static Device**: `HQ-SERVER` has static IP `192.168.40.10/28`.

### 5. VLAN 50: Guest Network (`192.168.50.0/24`)
- **Mask**: `255.255.255.0` (8 host bits: $2^8 - 2 = 254$ usable addresses)
- **Gateway**: `192.168.50.1` on `HQ-CORE` SVI `interface Vlan50`
- **Verified Host**: `GUEST-PC1` dynamically receives `192.168.50.2` via DHCP from `HQ-R1`.

### 6. Branch Subnets (`VLAN 60` and `VLAN 70`)
- **VLAN 60 (BR-USERS)**: `192.168.60.0/26` (Gateway `192.168.60.1` on `BR-R2 G0/0.60`)
  - **Management SVI**: `BR-SW1` is configured with `192.168.60.62` for SSHv2 access.
  - **Verified Host**: `BR-PC1` dynamically receives `192.168.60.2` via local DHCP on `BR-R2`.
- **VLAN 70 (BR-ADMIN)**: `192.168.70.0/26` (Gateway `192.168.70.1` on `BR-R2 G0/0.70`)
  - **Verified Host**: `BR-PC2` dynamically receives `192.168.70.2` via local DHCP on `BR-R2`.

### 7. Point-to-Point Links (`/30`)
- **HQ Transit (`10.255.0.0/30`)**:
  - `HQ-CORE Gi0/1`: `10.255.0.1`
  - `HQ-R1 G0/0`: `10.255.0.2`
- **Serial WAN (`10.0.0.0/30`)**:
  - `HQ-R1 S0/3/0`: `10.0.0.1`
  - `BR-R2 S0/3/0`: `10.0.0.2`
- **ISP Edge (`203.0.113.0/30`)**:
  - `HQ-R1 G0/1`: `203.0.113.1`
  - `ISP Fa0/0`: `203.0.113.2`

---

Next: [05 — VLAN Architecture](05-vlan-architecture.md)
