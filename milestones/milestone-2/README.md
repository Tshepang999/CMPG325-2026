# CMPG 325 - Milestone 2: Cisco Packet Tracer Implementation

## Project Details
* **Project ID:** CMPG325-2026-087
* **Client:** Kgomotso Marketing & Advertising Agency
* **Implementation Date:** October 2026

## 1. Network Overview & VLAN Design
This project implements a fully routed local network segmented into four functional VLANs using a Layer 3 Core Switch for Inter-VLAN routing, and an Edge Router providing a simulated remote administration boundary.

* **VLAN 10 (MANAGEMENT):** 10.36.10.0/24 (Gateway: 10.36.10.1)
* **VLAN 20 (STAFF):** 10.36.20.0/24 (Gateway: 10.36.20.1)
* **VLAN 30 (SERVERS):** 10.36.30.0/24 (Gateway: 10.36.30.1)
* **VLAN 40 (CONTRACTOR):** 10.36.40.0/24 (Gateway: 10.36.40.1)

## 2. Connectivity & Integration Testing Evidence

### Test A: Inter-VLAN Routing Verification
Verification showing successful local network communication from the Management domain (PC-ADMIN) across subnets to the primary corporate server (SRV1).
![Management to Server Ping](screenshots/fig1-lan-ping.png)

### Test B: Core Network to Edge Border Gateway Test
Verification proving traffic successfully exits the local switching core and reaches the outer corporate boundary at the Edge Router (`10.36.254.1`).
![Server to Edge Router Ping](screenshots/fig2-router-ping.png)

### Test C: Remote Administration over SSH & Fault-Isolation
Proof of secure terminal management. The external administrator terminal (`192.168.100.2`) successfully established an encrypted SSH connection to `R1-EDGE` using cryptographically generated 1024-bit RSA keys.
![Successful SSH Session](screenshots/fig3-ssh-success.png)

## 3. Configuration Source Files
The complete verified source code dumps can be reviewed here:
* [Edge Router Configuration](./R1-EDGE-config.txt)
* [L3 Core Switch Configuration](./SW1-CORE-config.txt)
* [Access Switch Configuration](./SW2-ACCESS-config.txt)
