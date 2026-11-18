# 12 — Testing, Verification & Test Matrices

## Testing Philosophy

In production network engineering, configurations cannot be assumed functional without systematic, multi-layer verification. 

Every single test case in this Master Verification Matrix has been executed and verified directly within Cisco Packet Tracer.

**Overall Status: 100% COMPLETE & VERIFIED.**

---

## Master Verification Matrix

| Test ID | Test Category | Target Devices / Paths | Verification Procedure / Commands | Expected Outcome | Actual Result | Status |
| :---: | :--- | :--- | :--- | :--- | :--- | :---: |
| **TC-01** | Physical Links | All devices | Inspect cable connections and link lights | Green link lights across all connected interfaces | All link lights green | **Verified** |
| **TC-02** | HWIC Modules | `HQ-R1`, `BR-R2` | `show ip interface brief` | `Serial0/3/0` present and recognized by IOS | Serial interfaces recognized | **Verified** |
| **TC-03** | Core VLANs | `HQ-CORE` | `show vlan brief` | VLANs 10, 20, 30, 40, and 50 present in database | All 5 VLANs present and active | **Verified** |
| **TC-04** | Core SVIs | `HQ-CORE` | `show ip interface brief` | SVIs 10, 20, 30, 40, and 50 configured with gateway IPs | All SVIs in `up/up` state | **Verified** |
| **TC-05** | HQ Switching | `HQ-CORE`, `HQ-ACC1`, `HQ-ACC2` | `show interfaces trunk` / `show vlan brief` | Trunks carry allowed VLANs; access ports assigned | Trunks and access ports operational | **Verified** |
| **TC-06** | Branch Switching | `BR-SW1` | `show interfaces trunk` / `show vlan brief` | Gi0/1 trunk active; Fa0/1 in VLAN 60, Fa0/2 in VLAN 70 | Trunk and access ports operational | **Verified** |
| **TC-07** | HQ Transit Link | `HQ-CORE` &harr; `HQ-R1` | `ping 10.255.0.2` from `HQ-CORE` | 100% ICMP ping reply across routed `/30` port | 5/5 ICMP replies received | **Verified** |
| **TC-08** | Serial WAN Link | `HQ-R1` &harr; `BR-R2` | `show ip interface brief` | `Serial0/3/0` status `UP/UP` on both routers | Line and protocol `UP/UP` | **Verified** |
| **TC-09** | Branch RoaS | `BR-R2` | `show ip interface brief` | `G0/0.60` and `G0/0.70` status `UP/UP` | Both subinterfaces `UP/UP` | **Verified** |
| **TC-10** | OSPF Neighbors | `HQ-R1`, `BR-R2`, `HQ-CORE` | `show ip ospf neighbor` | Adjacency reaches `FULL` state across serial & transit | Full adjacencies established | **Verified** |
| **TC-11** | OSPF Route Learning | `HQ-CORE` | `show ip route ospf` | Branch networks `192.168.60.0/26` and `.70.0/26` learned | Routes present via `10.255.0.2` | **Verified** |
| **TC-12** | Local Server HTTP | `HQ-SERVER` | Web browser to `http://192.168.40.10` | Packet Tracer welcome web page renders | Web page loaded successfully | **Verified** |
| **TC-13** | HQ DHCP Leases | HQ PCs (`HR`, `ENG`, `IT`, `GUEST`) | Desktop IP configuration set to DHCP | Workstations receive dynamic leases via Core DHCP relay | Bindings confirmed: `.10.2`, `.20.2`, `.30.2`, `.50.2` | **Verified** |
| **TC-14** | Branch DHCP Leases | Branch PCs (`BR-PC1`, `BR-PC2`) | Desktop IP configuration set to DHCP | Workstations receive dynamic leases via local BR-R2 DHCP | Bindings confirmed: `.60.2`, `.70.2` | **Verified** |
| **TC-15** | Inter-VLAN Routing | `HR-PC1` &rarr; `IT-PC1` | `ping 192.168.30.2` from `192.168.10.2` | ICMP replies returned across `HQ-CORE` SVIs | 100% ping success | **Verified** |
| **TC-16** | Cross-WAN Reachability | `HR-PC1` &rarr; `BR-PC1` | `ping 192.168.60.2` from `192.168.10.2` | ICMP replies returned across serial WAN link | 100% ping success | **Verified** |
| **TC-17** | Branch to Server Access | `BR-PC1` &rarr; `HQ-SERVER` | `ping 192.168.40.10` from `192.168.60.2` | ICMP replies returned from internal server | 100% ping success | **Verified** |
| **TC-18** | Guest ACL Isolation | `GUEST-PC1` &rarr; `IT-PC1` | `ping 192.168.30.2` from `192.168.50.2` | Packet rejected by `GUEST_ONLY_INTERNET` ACL | Failed with `Destination host unreachable` from `192.168.50.1` | **Verified** |
| **TC-19** | Guest Internet Access | `GUEST-PC1` &rarr; `8.8.8.8` | `ping 8.8.8.8` from `192.168.50.2` | Traffic permitted by ACL & routed via ISP | Successful after adding HQ-CORE default route | **Verified** |
| **TC-20** | Live NAT Translation Table | `HQ-R1` | `show ip nat translations` | View active translation sessions in NAT table | Translation entry: `192.168.50.2` &rarr; `203.0.113.1` | **Verified** |
| **TC-21** | Switch Port Security | User access ports | `show port-security address` / interface status | Access ports learn SecureSticky MACs; violation shutdown | Sticky MACs learned on all switches | **Verified** |
| **TC-22** | SSHv2 Remote Login | `HR-PC1` &rarr; `HQ-ACC1` | `ssh -l admin 192.168.10.62` | Encrypted prompt established; Telnet rejected | Admin CLI session opened successfully | **Verified** |

---

## Detailed Notes on Verified Test Results

### 1. Inter-VLAN Routing (`HR-PC1` &rarr; `IT-PC1`)
- **Action**: From `HR-PC1` (`192.168.10.2`), executed `ping 192.168.30.2`.
- **Result**: 100% ICMP replies returned.
- **Significance**: Proves that `HQ-CORE` SVIs route departmental packets at wire speed in hardware.

### 2. Cross-Site WAN Reachability (`HR-PC1` &rarr; `BR-PC1` & `BR-PC1` &rarr; `HQ-SERVER`)
- **Action 1**: `HR-PC1` pinged `BR-PC1` (`192.168.60.2`) across the serial WAN.
- **Action 2**: `BR-PC1` pinged `HQ-SERVER` (`192.168.40.10`).
- **Result**: 100% success on both paths.
- **Significance**: Confirms end-to-end integration of Core switching, OSPF dynamic routing, serial WAN transmission, and branch Router-on-a-Stick subinterfaces.

![HQ to Branch Connectivity](../screenshots/14-connectivity-hq-to-branch.png)

### 3. Guest ACL Isolation (`GUEST-PC1` &rarr; `IT-PC1`)
- **Action**: From `GUEST-PC1` (`192.168.50.2`), executed `ping 192.168.30.2`.
- **Result**: `Destination host unreachable` returned by gateway `192.168.50.1`.
- **Significance**: Proves that the `GUEST_ONLY_INTERNET` ACL enforces a zero-trust boundary, stopping unauthorized lateral movement into corporate subnets.

![Guest ACL Test](../screenshots/11-acl-test.png)

### 4. Guest Internet Access & NAT/PAT (`GUEST-PC1` &rarr; `8.8.8.8`)
- **Action**: From `GUEST-PC1`, executed `ping 8.8.8.8`.
- **Result**: 100% ICMP replies returned.
- **NAT Table Capture**: Executing `show ip nat translations` on `HQ-R1` captured the active ICMP session mapping `192.168.50.2` to `203.0.113.1`.

![NAT PAT Verification](../screenshots/10-nat-pat-verification.png)
![Guest Internet Success](../screenshots/11-guest-internet-success.png)

### 5. Switchport Security Verification
- **Action**: Verified `show port-security interface <port>` on `HQ-ACC1`, `HQ-ACC2`, and `BR-SW1`.
- **Result**: Ports are in `Secure-up` state with learned `SecureSticky` hardware addresses.

![Port Security on HQ-ACC1](../screenshots/12-port-security-hq-acc1-final.png)

### 6. SSHv2 Remote Management Login
- **Action**: Connected from `HR-PC1` command line via `ssh -l admin 192.168.10.62`.
- **Result**: Password prompt authenticated the session, and privileged EXEC mode (`HQ-ACC1#`) was opened.

![SSH Login Success](../screenshots/13-ssh-login-success.png)

---

Next: [13 — Troubleshooting Log](13-troubleshooting.md)
