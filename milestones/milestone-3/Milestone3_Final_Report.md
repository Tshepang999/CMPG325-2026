# Milestone 3 — Final Project Report
## Network Implementation, Security, Testing and Fault Isolation

---

### 📢 CRITICAL DELIVERABLE LINKS
* **Interactive Simulation File:** [ packet-tracer/CMPG325_M3_Final_Corrected.pkt ](../../packet-tracer/)
* **Project Presentation Video:** [ 🎥 Watch the 15-20 Minute Video Demonstration & Technical Defence ](https://google.com)

---

| Project information   | Details   |
| :--- | :--- |
| **Project ID**   | CMPG325-2026-087   |
| **Client ID**   | CLI-087   |
| **Client**   | Kgomotso Marketing & Advertising Agency   |
| **Client location**   | Rustenburg, North West, South Africa   |
| **Student**   | T. Mosia   |
| **Student number**   | 44368968   |
| **Addressing block**   | 10.36.0.0/16   |
| **Assigned challenge**   | Network Troubleshooting — Fault Isolation Scenario   |
| **Special requirements** | Limited after-hours contractor wireless access; CR9 secure remote management for one off-site administrator   |
| **Submission stage**   | Milestone 3 — Final project report   |
| **Report preparation date** | 15 October 2026   |

---

## 1. Executive Summary
This project designs and implements a segmented network for Kgomotso Marketing & Advertising Agency in Rustenburg. The solution uses the allocated 10.36.0.0/16 address block and separates management, staff, server and contractor traffic into four VLANs. The project also addresses contractor wireless isolation, secure remote administration for one authorised off-site administrator (CR9), and the assigned network troubleshooting challenge.

The intended routing design recorded in the Milestone 2 report is router-on-a-stick: R1-EDGE provides 802.1Q subinterfaces and the default gateway for each VLAN. Switches SW1-CORE and SW2-ACCESS carry VLANs over trunk links and provide access-port connectivity. Contractor traffic is intended to be prevented from reaching internal management, staff and server subnets while allowing only the external access required by the client. Secure remote management uses SSH, local authentication and a VTY access restriction.

The final evaluation demonstrates the working topology, addressing and routing, contractor access boundary, CR9 remote access, and the fault-isolation process. The evidence is captured from Cisco Packet Tracer and aligned with the submitted .pkt file.

---

## 2. Client Requirements and Project Objectives

| Requirement   | Project response   |
| :--- | :--- |
| **Use allocated addressing block**   | Use subnets within 10.36.0.0/16.   |
| **Separate business traffic**   | Use VLAN 10 MANAGEMENT, VLAN 20 STAFF, VLAN 30 SERVERS and VLAN 40 CONTRACTOR.   |
| **Provide connectivity**   | Configure gateways and routing so authorised management/staff traffic can reach permitted destinations.   |
| **Restrict contractors**   | Isolate VLAN 40 from VLANs 10, 20 and 30; allow only the external access actually required and configured.   |
| **CR9 remote management**   | Use SSH only, local user authentication and a VTY access list restricting management access to the authorised off-site administrator.   |
| **Fault-isolation challenge**   | Introduce a controlled staff-port VLAN mismatch, identify it using show commands, correct it and retest.   |
| **Evidence and portfolio**   | Maintain the Packet Tracer project, device configurations, test results, screenshots and report in the GitHub portfolio.   |

---

## 3. Logical Addressing and VLAN Plan

| VLAN / Interface | Name / Connection | Network | Default Gateway | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **VLAN 10** | MANAGEMENT | 10.36.10.0/24 | 10.36.10.1 | Network administration and management hosts. |
| **VLAN 20** | STAFF | 10.36.20.0/24 | 10.36.20.1 | Employee workstations. |
| **VLAN 30** | SERVERS | 10.36.30.0/24 | 10.36.30.1 | Server resources and centralized agency services. |
| **VLAN 40** | CONTRACTOR | 10.36.40.0/24 | 10.36.40.1 | Isolated wireless clients for after-hours contractors. |
| **VLAN 99 / Sub** | TRANSIT / NATIVE | 10.36.254.0/24 | 10.36.254.1 | Core network transit infrastructure and edge gateway path. |
| **R1-EDGE G0/1** | EXTERNAL WAN | 192.168.100.0/24 | 192.168.100.1 | Simulated public internet link for secure CR9 remote management. |

The network subnets listed above represent the complete operational addressing scheme carried forward and refined from the Milestone 2 design. End-device addresses, static server assignments, and DHCP scopes strictly adhere to this matrix to ensure uniform validation throughout the topology testing phase.

---

## 4. Topology and Device Roles

| Device / component   | Role and expected responsibility   |
| :--- | :--- |
| **R1-EDGE**   | Edge router; router-on-a-stick VLAN gateways, inter-VLAN routing, ACL enforcement, any configured NAT/PAT, and SSH management.   |
| **SW1-CORE**   | Core switch; VLAN database, trunking, management SVI and switch management settings.   |
| **SW2-ACCESS**   | Access switch; endpoint access ports, VLAN assignments, trunk uplink and management settings.   |
| **SRV1**   | Server in VLAN 30; static addressing and DNS/services only where configured.   |
| **AP-CONTRACTOR**   | Wireless access point mapped to the contractor VLAN.   |
| **PC-ADMIN**   | Management workstation in VLAN 10.   |
| **PC-STAFF01 / PC-STAFF02**   | Staff workstations in VLAN 20.   |
| **CONTRACTOR-LAPTOP**   | Wireless contractor endpoint in VLAN 40.   |
| **OFF-SITE-ADMIN**   | External test endpoint used to demonstrate authorised SSH access, subject to the simulated topology.   |

---

## 5. Implementation Summary

### 5.1 VLANs, access ports and trunking
* Create VLANs 10, 20, 30 and 40 with the names shown in the VLAN plan.
* Assign management and staff endpoints to their intended access VLANs.
* Map the contractor wireless access point/client traffic to VLAN 40.
* Configure trunk links between the router/core/access devices as required by the actual topology, allowing the required VLANs.
* Verify using `show vlan brief` and `show interfaces trunk` on the core and access devices.

### 5.2 Inter-VLAN routing
The network implements a router-on-a-stick topology on R1-EDGE using 802.1Q subinterfaces `g0/0.10`, `g0/0.20`, `g0/0.30`, and `g0/0.40`. The configured subinterface gateway addresses are mapped as follows:
* `g0/0.10`: `10.36.10.1/24` (MANAGEMENT)
* `g0/0.20`: `10.36.20.1/24` (STAFF)
* `g0/0.30`: `10.36.30.1/24` (SERVERS)
* `g0/0.40`: `10.36.40.1/24` (CONTRACTOR)

### 5.3 Address assignment and server services
End-devices obtain network parameters within their respective /24 subnets. Static IP mappings are maintained for core infrastructure assets, including `SRV1` (`10.36.30.10`) inside the server environment. This uniform layout ensures stable infrastructure state logging during active routing sessions.

---

## 6. Security Implementation

### 6.1 Contractor network isolation
VLAN 40 strictly isolates guest contractor wireless devices from internal administrative segments. An **Extended Access Control List (ACL)** is bound inbound on the `g0/0.40` interface of R1-EDGE. This policy denies transit packets originating from `10.36.40.0/24` targeting the internal subnets (`10.36.10.0/24`, `10.36.20.0/24`, and `10.36.30.0/24`), while allowing standard external access paths out to the simulated public internet link.

### 6.2 CR9 — secure remote management
Secure administrative parameters are enforced on R1-EDGE to achieve the CR9 secure remote management benchmark:
* **SSH Enforcement:** SSH version 2 is active on the device, with unencrypted Telnet completely deactivated across all virtual terminal paths via `transport input ssh`.
* **Access Restraints:** A standard VTY access-class configuration filters structural access. Incoming administration attempts are rejected at the edge line unless they explicitly match the public WAN source IP address of the **OFF-SITE-ADMIN** (`192.168.100.2`).
* **Authentication:** Strong local credential authorization parameters are stored using type-5 secret parameters to guarantee data security.

---

## 7. Fault-Isolation Demonstration

The troubleshooting exercise tracks a Layer 2 domain error caused by an accidental port assignment on access layer infrastructure. The entire lifecycle is validated using a systematic seven-step fault-isolation process:

1. **Establish Baseline:** A baseline connectivity test is run from `PC-STAFF01` to its default gateway at `10.36.20.1`. The ICMP transaction succeeds immediately with zero packet loss.
2. **Inject Fault:** The interface port hosting `PC-STAFF01` on `SW2-ACCESS` is intentionally reassigned to an incorrect broadcast domain, mismatching the staff subnet.
3. **Connectivity Failure:** Retesting connectivity from `PC-STAFF01` results in systematic request timeouts, indicating an interruption in local access processing.
4. **Isolate Error Layer:** Executing `show vlan brief` on the local access switch reveals the interface mismatch.
5. **Verify Transit Paths:** Running `show interfaces trunk` validates that core inter-switch trunk links and subinterface loops on the gateway router remain upright. This localizes the bug to the single port access boundary.
6. **Remediation Fix:** The switch port interface is returned to `switchport access vlan 20`, re-aligning it with its native domain matrix.
7. **Retest Operations:** Running `ping 10.36.20.1` confirms that bidirectional transit behavior is instantly restored.

---

## 8. Test and Verification Record

| ID   | Test Description | Expected Outcome | Observed Result / Status |
| :--- | :--- | :--- | :--- |
| **T01** | PC-ADMIN ➔ 10.36.10.1 | Gateway Ping succeeds | SUCCESS (Verified in Figure 1) |
| **T02** | PC-STAFF01 ➔ 10.36.20.1 | Gateway Ping succeeds | SUCCESS (Verified in Figure 2) |
| **T03** | PC-STAFF01 ➔ SRV1 | Internal Server Ping succeeds | SUCCESS (Verified in Figure 2) |
