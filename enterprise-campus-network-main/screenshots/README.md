# Cisco Packet Tracer Verification Screenshots Directory

This directory contains visual proof of the fully configured and tested enterprise campus network. All 27 screenshots were captured directly from the Cisco Packet Tracer CLI terminal and graphical workspace.

---

## Table of Contents by Category

- [1. Topology & Workspace Overview](#1-topology--workspace-overview)
- [2. Layer-2 Switching & 802.1Q Trunking](#2-layer-2-switching--8021q-trunking)
- [3. Dynamic Routing (OSPF Area 0) & WAN](#3-dynamic-routing-ospf-area-0--wan)
- [4. DHCP Architecture & Client Allocations](#4-dhcp-architecture--client-allocations)
- [5. Enterprise Intranet Server (HTTP & DNS)](#5-enterprise-intranet-server-http--dns)
- [6. Perimeter NAT/PAT & Internet Edge](#6-perimeter-natpat--internet-edge)
- [7. Guest Isolation Access Control (ACL)](#7-guest-isolation-access-control-acl)
- [8. Switchport Security (Sticky MAC Learning)](#8-switchport-security-sticky-mac-learning)
- [9. Encrypted Administration (SSHv2 Login)](#9-encrypted-administration-sshv2-login)
- [10. End-to-End Cross-Site Connectivity](#10-end-to-end-cross-site-connectivity)

---

## 1. Topology & Workspace Overview

Evidence documenting device placement, modular hardware installation, and operational link carrier states.

| Screenshot File | Description | Relevant Documentation |
| :--- | :--- | :--- |
| [`01-physical-topology.png`](01-physical-topology.png) | Initial physical cabling layout, showing all routers, multilayer switches, access switches, server, and workstations. | [02-topology.md](../docs/02-topology.md) |
| [`15-final-topology.png`](15-final-topology.png) | Complete, converged final topology with operational green link lights across all campus, WAN, and ISP links. | [02-topology.md](../docs/02-topology.md) |

---

## 2. Layer-2 Switching & 802.1Q Trunking

Evidence validating departmental broadcast domain segmentation, multilayer SVIs, and trunk link encapsulation.

| Screenshot File | Target Device | Verification Details | Relevant Documentation |
| :--- | :--- | :--- | :--- |
| [`02-hq-core-vlans.png`](02-hq-core-vlans.png) | `HQ-CORE` | `show vlan brief` confirming creation and active status of VLANs 10, 20, 30, 40, and 50. | [05-vlan-architecture.md](../docs/05-vlan-architecture.md) |
| [`03-hq-core-svis.png`](03-hq-core-svis.png) | `HQ-CORE` | `show ip interface brief` confirming SVI default gateways in the `up/up` line state. | [06-hq-core.md](../docs/06-hq-core.md) |
| [`04-hq-core-trunk-verification.png`](04-hq-core-trunk-verification.png) | `HQ-CORE` | `show interfaces trunk` verifying 802.1Q encapsulation and allowed VLAN filtering on `Gi0/2` (10, 20) and `Fa0/1` (30, 50). | [05-vlan-architecture.md](../docs/05-vlan-architecture.md) |

---

## 3. Dynamic Routing (OSPF Area 0) & WAN

Evidence proving neighbor adjacency formation, serial leased-line connectivity, and automated route propagation.

| Screenshot File | Target Device | Verification Details | Relevant Documentation |
| :--- | :--- | :--- | :--- |
| [`08-ospf-hq-core-neighbor.png`](08-ospf-hq-core-neighbor.png) | `HQ-CORE` | `show ip ospf neighbor` confirming `FULL` state adjacency with border router `HQ-R1` across transit link `10.255.0.0/30`. | [07-routing-and-wan.md](../docs/07-routing-and-wan.md) |
| [`08-ospf-neighbor-hq-r1.png`](08-ospf-neighbor-hq-r1.png) | `HQ-R1` | `show ip ospf neighbor` confirming dual `FULL` adjacencies with `HQ-CORE` and `BR-R2`. | [07-routing-and-wan.md](../docs/07-routing-and-wan.md) |
| [`09-ospf-branch-routes.png`](09-ospf-branch-routes.png) | `HQ-CORE` | `show ip route ospf` proving dynamic learning of branch subnets (`192.168.60.0/26` and `192.168.70.0/26`) via `10.255.0.2`. | [06-hq-core.md](../docs/06-hq-core.md) |
| [`14-ospf-final.png`](14-ospf-final.png) | Campus Routers | Final converged routing table demonstrating end-to-end reachability across the enterprise AS. | [07-routing-and-wan.md](../docs/07-routing-and-wan.md) |

---

## 4. DHCP Architecture & Client Allocations

Evidence verifying centralized HQ DHCP pools on `HQ-R1`, DHCP Relay helpers on `HQ-CORE`, local branch pools on `BR-R2`, and successful client lease acquisition.

| Screenshot File | Target View | Verification Details | Relevant Documentation |
| :--- | :--- | :--- | :--- |
| [`07-dhcp-bindings.png`](07-dhcp-bindings.png) | `HQ-R1` CLI | `show ip dhcp binding` showing active dynamic leases allocated to HQ workstations across departmental pools. | [08-dhcp-and-services.md](../docs/08-dhcp-and-services.md) |
| [`07-dhcp-hr-client.png`](07-dhcp-hr-client.png) | `HR-PC1` Desktop | `ipconfig` output confirming IP `192.168.10.2`, mask `255.255.255.192`, gateway `192.168.10.1`, and DNS `192.168.40.10`. | [08-dhcp-and-services.md](../docs/08-dhcp-and-services.md) |
| [`07-dhcp-guest-client.png`](07-dhcp-guest-client.png) | `GUEST-PC1` Desktop | `ipconfig` output confirming dynamic IP `192.168.50.2` and gateway `192.168.50.1`. | [08-dhcp-and-services.md](../docs/08-dhcp-and-services.md) |
| [`08-dhcp-hq-final.png`](08-dhcp-hq-final.png) | `HQ-R1` CLI | Overview of operational HQ DHCP pools (`HR`, `ENGINEERING`, `IT`, `GUEST`). | [08-dhcp-and-services.md](../docs/08-dhcp-and-services.md) |
| [`07-dhcp-branch-bindings.png`](07-dhcp-branch-bindings.png) | `BR-R2` CLI | `show ip dhcp binding` verifying dynamic leases allocated to branch workstations from local pools. | [08-dhcp-and-services.md](../docs/08-dhcp-and-services.md) |
| [`07-dhcp-branch-clients.png`](07-dhcp-branch-clients.png) | Branch PCs | Command prompt `ipconfig` output on branch client workstations confirming received lease parameters. | [08-dhcp-and-services.md](../docs/08-dhcp-and-services.md) |
| [`08-dhcp-branch-final.png`](08-dhcp-branch-final.png) | `BR-R2` CLI | Overview of operational branch DHCP pools (`BR-USERS`, `BR-ADMIN`). | [08-dhcp-and-services.md](../docs/08-dhcp-and-services.md) |

---

## 5. Enterprise Intranet Server (HTTP & DNS)

Evidence validating static server addressing, DNS resolution, and local web portal hosting.

| Screenshot File | Target View | Verification Details | Relevant Documentation |
| :--- | :--- | :--- | :--- |
| [`06-server-http.png`](06-server-http.png) | Desktop Web Browser | Packet Tracer web browser accessing `http://192.168.40.10` and rendering the corporate welcome page. | [08-dhcp-and-services.md](../docs/08-dhcp-and-services.md) |

---

## 6. Perimeter NAT/PAT & Internet Edge

Evidence verifying Port Address Translation (NAT Overload) on `HQ-R1` and simulated Internet egress.

| Screenshot File | Target View | Verification Details | Relevant Documentation |
| :--- | :--- | :--- | :--- |
| [`10-nat-pat-verification.png`](10-nat-pat-verification.png) | `HQ-R1` CLI | `show ip nat translations` capturing active translation: `192.168.50.2` translated to `203.0.113.1` reaching `8.8.8.8`. | [09-nat-and-acls.md](../docs/09-nat-and-acls.md) |
| [`11-guest-internet-success.png`](11-guest-internet-success.png) | `GUEST-PC1` CLI | 100% successful ICMP ping to simulated Internet host `8.8.8.8` after resolving the `HQ-CORE` default route. | [09-nat-and-acls.md](../docs/09-nat-and-acls.md) |

---

## 7. Guest Isolation Access Control (ACL)

Evidence proving zero-trust guest containment enforced by the `GUEST_ONLY_INTERNET` extended ACL.

| Screenshot File | Target View | Verification Details | Relevant Documentation |
| :--- | :--- | :--- | :--- |
| [`11-acl-test.png`](11-acl-test.png) | `GUEST-PC1` CLI | Ping to `IT-PC1` (`192.168.30.2`) blocked by ACL, returning `Destination host unreachable` from gateway `192.168.50.1`. | [09-nat-and-acls.md](../docs/09-nat-and-acls.md) |

---

## 8. Switchport Security (Sticky MAC Learning)

Evidence validating Layer-2 port hardening, maximum MAC limits (2), sticky MAC learning, and shutdown violation mode.

| Screenshot File | Target Device | Verification Details | Relevant Documentation |
| :--- | :--- | :--- | :--- |
| [`12-port-security-hq-acc1-final.png`](12-port-security-hq-acc1-final.png) | `HQ-ACC1` | Summary overview confirming active port security on access ports `Fa0/1` through `Fa0/5`. | [10-switch-security.md](../docs/10-switch-security.md) |
| [`12-port-security-hq-acc1.png`](12-port-security-hq-acc1.png) | `HQ-ACC1` | `show port-security address` table showing dynamically learned `SecureSticky` PC MAC addresses. | [10-switch-security.md](../docs/10-switch-security.md) |
| [`12-port-security-hq-acc2.png`](12-port-security-hq-acc2.png) | `HQ-ACC2` | Port security table on `HQ-ACC2` confirming learned sticky MAC addresses on `Fa0/1` through `Fa0/3`. | [10-switch-security.md](../docs/10-switch-security.md) |
| [`12-port-security-br-sw1.png`](12-port-security-br-sw1.png) | `BR-SW1` | `show port-security interface` output confirming SecureSticky learning and shutdown violation mode on `Fa0/1`–`Fa0/2`. | [10-switch-security.md](../docs/10-switch-security.md) |
| [`13-ssh-and-port-security-final.png`](13-ssh-and-port-security-final.png) | Access Switch | Combined terminal view demonstrating both active port security parameters and running SSHv2 configuration. | [10-switch-security.md](../docs/10-switch-security.md) |

---

## 9. Encrypted Administration (SSHv2 Login)

Evidence confirming administrative plane encryption, VTY lockout of Telnet, and successful remote authentication.

| Screenshot File | Target View | Verification Details | Relevant Documentation |
| :--- | :--- | :--- | :--- |
| [`13-ssh-login-success.png`](13-ssh-login-success.png) | `HR-PC1` CLI | Live session output establishing `ssh -l admin 192.168.10.62` into `HQ-ACC1` and authenticating to privileged EXEC prompt. | [11-ssh-management.md](../docs/11-ssh-management.md) |

---

## 10. End-to-End Cross-Site Connectivity

Evidence proving comprehensive reachability across departmental SVIs, the routed campus transit, and the serial WAN.

| Screenshot File | Target View | Verification Details | Relevant Documentation |
| :--- | :--- | :--- | :--- |
| [`14-connectivity-hq-to-branch.png`](14-connectivity-hq-to-branch.png) | Client PC CLI | Multi-hop ping sweep demonstrating reachability from HQ workstations to branch workstations across the serial WAN. | [12-testing-and-verification.md](../docs/12-testing-and-verification.md) |
