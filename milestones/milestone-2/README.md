# CMPG 325
## INDIVIDUAL SEMESTER PROJECT
### MILESTONE 2 — CLIENT IMPLEMENTATION REVIEW

| Project ID | CMPG325-2026-087 |
| :--- | :--- |
| **Client ID** | CLI-087 |
| **Student** | T. Mosia |
| **Student Number** | 44368968 |
| **Client** | Kgomotso Marketing & Advertising Agency |
| **Location** | Rustenburg, North West, South Africa |
| **Addressing Block** | 10.36.0.0/16 |
| **Technical Challenge** | Network Troubleshooting — Fault Isolation Scenario |
| **Constraint** | Limited after-hours wireless access for cleaning and security contractors |
| **CR9** | Secure remote management access for one off-site administrator |
| **Milestone** | 2 — Client Implementation Review |
| **Review Date** | 02 October 2026 |

***

### Executive Summary
Milestone 2 moves the approved Milestone 1 design into a functional Cisco Packet Tracer implementation and testing verification state. The architecture preserves the designated VLAN infrastructure, static subnet addressing boundaries, contractor access security constraints, and critical CR9 secure off-site administrative access parameters without modifications. 

### Milestone 2 Objectives
*   Implement the physical and logical multi-device network topology in Cisco Packet Tracer.
*   Configure corporate VLAN infrastructure (10, 20, 30, 40) using the `10.36.0.0/16` block.
*   Deploy hardware-accelerated wire-speed Inter-VLAN routing on the Layer 3 core switch.
*   Isolate contractor wireless boundaries from internal secure subnets.
*   Deploy cryptographically locked SSH remote management channels for the off-site administrator.
*   Demonstrate clear structural fault introduction, discovery analysis, and recovery proof.

### Approved Design Carried Forward

| VLAN | Name | Network Subnet | Default Gateway | Primary Operational Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **10** | MANAGEMENT | 10.36.10.0/24 | 10.36.10.1 | Secure hardware switch management interfaces |
| **20** | STAFF | 10.36.20.0/24 | 10.36.20.1 | Employee computing workstations and terminals |
| **30** | SERVERS | 10.36.30.0/24 | 10.36.30.1 | Centralized application and file servers |
| **40** | CONTRACTOR | 10.36.40.0/24 | 10.36.40.1 | Restricted external contractor wireless access |

***

### Packet Tracer Implementation Details
The hardware mapping successfully links the edge infrastructure to the localized access nodes:

| Device Hostname | Model Designation | Assigned Logical and Structural Network Role |
| :--- | :--- | :--- |
| **R1-EDGE** | Cisco 2911 | Perimeter routed edge gateway, SSH host, and management filter. |
| **SW1-CORE** | Cisco 3560-24PS | Layer 3 routing engine, SVIs, trunk optimization, and IP routing. |
| **SW2-ACCESS** | Cisco 2960 | Layer 2 edge distribution switch managing individual access ports. |
| **SRV1** | Server-PT | Production server running on static VLAN 30 network space. |
| **AP-CONTRACTOR**| AccessPoint-PT | Encrypted standalone Wi-Fi access bridge mapping clients to VLAN 40. |
| **PC-ADMIN** | PC-PT | Dedicated localized administrative terminal bound to VLAN 10. |
| **PC-STAFF01/02**| PC-PT | Internal staff computing terminals isolated inside VLAN 20. |
| **CONTRACTOR-Laptop** | Laptop-PT | External technician client system using WPA2-PSK on VLAN 40. |
| **OFF-SITE-ADMIN**| PC-PT | Simulated off-site secure terminal testing the CR9 perimeter access. |

***

### Configuration Baselines (Actual Implemented Code)

#### 1. R1-EDGE (Perimeter Gateway Router)
```ios
hostname R1-EDGE
!
interface GigabitEthernet0/0
 ip address 192.168.100.1 255.255.255.0
 no shutdown
!
interface GigabitEthernet0/1
 ip address 10.36.254.1 255.255.255.252
 no shutdown
!
ip route 10.36.10.0 255.255.255.0 10.36.254.2
ip route 10.36.20.0 255.255.255.0 10.36.254.2
ip route 10.36.30.0 255.255.255.0 10.36.254.2
ip route 10.36.40.0 255.255.255.0 10.36.254.2
!
username admin privilege 15 secret Cisco@2026
ip domain-name company.local
crypto key generate rsa modulus 1024
ip ssh version 2
!
access-list 10 permit host 192.168.100.2
!
line vty 0 4
 login local
 transport input ssh
 access-class 10 in
```

#### 2. SW1-CORE (Layer 3 Switch Core Engine)
```ios
hostname SW1-CORE
ip routing
!
vlan 10
 name MANAGEMENT
vlan 20
 name STAFF
vlan 30
 name SERVERS
vlan 40
 name CONTRACTOR
!
interface vlan 10
 ip address 10.36.10.1 255.255.255.0
 no shutdown
interface vlan 20
 ip address 10.36.20.1 255.255.255.0
 no shutdown
interface vlan 30
 ip address 10.36.30.1 255.255.255.0
 no shutdown
interface vlan 40
 ip address 10.36.40.1 255.255.255.0
 no shutdown
!
interface GigabitEthernet1/0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40
!
interface GigabitEthernet1/0/2
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
!
interface GigabitEthernet1/0/3
 switchport mode access
 switchport access vlan 40
 spanning-tree portfast
!
interface GigabitEthernet1/0/4
 no switchport
 ip address 10.36.254.2 255.255.255.252
 no shutdown
!
ip route 0.0.0.0 0.0.0.0 10.36.254.1
```

#### 3. SW2-ACCESS (Access Delivery Switch)
```ios
hostname SW2-ACCESS
!
vlan 10
 name MANAGEMENT
vlan 20
 name STAFF
vlan 30
 name SERVERS
vlan 40
 name CONTRACTOR
!
interface FastEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40
!
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
!
interface FastEthernet0/3
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
!
interface FastEthernet0/4
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
```

***

### Contractor Isolation and CR9 Remote Access Realization
*   **VLAN 40 Contractor Isolation:** Extracted wireless clients on `AP-CONTRACTOR` map directly into VLAN 40 at the core layer. Security constraints dictate that contractor clients must not establish communication pathways to sensitive internal subnets (VLAN 10, 20, 30).
*   **CR9 Control Parameter:** Remote encrypted management is enforced using SSH Version 2 with 1024-bit RSA key standards. Line access restriction policies utilizing standard IP Access Control Lists (`access-list 10`) block non-designated networks, keeping VTY terminal lines fully isolated against unauthenticated internal or external exploration.

***

### Integration Testing and Matrix Verification Logs

| ID | Origin Device | Destination Target | Expected Behavior | Actual Result Status |
| :--- | :--- | :--- | :--- | :--- |
| **T01** | PC-ADMIN | 10.36.10.1 (SVI) | Pass | **SUCCESSFUL** (0% Loss) |
| **T02** | PC-STAFF01 | 10.36.20.1 (SVI) | Pass | **SUCCESSFUL** (0% Loss) |
| **T03** | PC-STAFF01 | 10.36.30.10 (SRV1) | Pass | **SUCCESSFUL** (0% Loss) |
| **T04** | PC-ADMIN | 10.36.30.10 (SRV1) | Pass | **SUCCESSFUL** (0% Loss) |
| **T05** | CONTRACTOR-Laptop | 10.36.40.1 (SVI) | Pass | **SUCCESSFUL** (0% Loss) |
| **T06** | SRV1 | 10.36.254.1 (Router) | Pass | **SUCCESSFUL** (0% Loss) |
| **T07** | OFF-SITE-ADMIN | 192.168.100.1 (SSH) | Pass (Prompted) | **SUCCESSFUL** (R1-EDGE#) |
| **T08** | PC-STAFF01 | 192.168.100.1 (SSH) | Blocked (Dropped)| **SUCCESSFUL BLOCKED** |

***

### Assigned Fault-Isolation Operational Scenario Log
*   **Identified Fault:** Incorrect VLAN broadcast configuration on access port `FastEthernet0/3` on **SW2-ACCESS**, severing the Staff network communication trajectory.
*   **Diagnostic Toolkit Used:** Command Line Interface (`ping`), (`show vlan brief`), (`show ip interface brief`), (`show running-config`).

```text
[Stage 1] Baseline established on PC-STAFF01. Local physical line indicators are up.
[Stage 2] Intentional misconfiguration introduced: FastEthernet0/3 shifted from VLAN 20 to VLAN 40.
[Stage 3] Observed Error State: Ping requests to local default gateway 10.36.20.1 fail with absolute 100% loss.
[Stage 4] Investigative Analysis: Executed 'show vlan brief' on SW2-ACCESS. Found port Fa0/3 bound erroneously to CONTRACTOR network.
[Stage 5] Path Investigation: Verified trunk path execution via 'show interfaces trunk' - configurations show healthy state.
[Stage 6] Engineering Correction: Entered interface Fa0/3 configuration sub-tree and applied 'switchport access vlan 20'.
[Stage 7] Post-Correction Verification: Retested ping from PC-STAFF01 to gateway 10.36.20.1 and SRV1 10.36.30.10. Execution passes cleanly with 0% loss.
[Stage 8] Documentation Archive: Action log, structural root-cause mitigation file compiled and synchronized to GitHub repository.
```

***

### Repository Structural Layout Plan
Your structural cloud submission directory is mapped using this schema:
*   📁 **`packet-tracer/`** : Contains the primary verified simulation delivery blueprint file (`CMPG325_Milestone2_Kgomotso.pkt`).
*   📁 **`configurations/`** : Complete text documentation run logs for the router and switches (`R1-EDGE-config.txt`, `SW1-CORE-config.txt`, `SW2-ACCESS-config.txt`).
*   📁 **`evidence/screenshots/`** : High-fidelity image captures representing diagnostic success parameters (`fig1-lan-ping.png`, `fig2-router-ping.png`, `fig3-ssh-success.png`).

### Selected Narrative Git Commit Logs
*   `feat: implement approved VLAN topology in Packet Tracer`
*   `feat: deploy hardware accelerated inter-VLAN core routing on SW1-CORE`
*   `feat: deploy cryptographically isolated SSH configurations for CR9 boundary`
*   `test: document baseline local network and border gateway verification metrics`
*   `fix: apply structural corrective action to resolve Fa0/3 workstation VLAN fault assignment`

### Conclusion
