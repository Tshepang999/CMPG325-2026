# CMPG 325 Computer Networks — Milestone 3 Portfolio
**Student Name:** T. Mosia  
**Student Number:** 44368968  
**Project ID:** CMPG325-2026-087  
**Client:** Kgomotso Marketing & Advertising Agency (Rustenburg, South Africa)

---

## 📺 Final Project Video Demonstration & Technical Defence
🎥 **Watch the Live Presentation:** [Click Here to View the Video Demonstration](https://drive.google.com/file/d/1nYh68SyYhiaVUEW2oTxPtTniekzyMd6i/view?usp=sharing)  
*This 15–20 minute recording walks through the working Cisco Packet Tracer topology, validates our departmental security ACLs, confirms the CR9 remote management parameters, and demonstrates a live network fault isolation scenario.*

---

## 📌 Portfolio Overview
This repository contains the complete network planning, security implementation, verification testing, and troubleshooting logs for the Kgomotso Marketing & Advertising Agency corporate network. The solution utilizes the assigned **10.36.0.0/16** addressing block to achieve departmental segmentation, wireless guest contractor isolation, and secure off-site remote management.

---

## 📂 Repository Directory Structure
* 📄 **`README.md`** - Main project dashboard and deployment instructions (This page).
* 📁 **`packet-tracer/`** - Contains the final, fully working `.pkt` simulation file.
* 📁 **`configurations/`** - Text exports of final running configs for R1-EDGE, SW1-CORE, and SW2-ACCESS.
* 📁 **`testing/`** - Completed verification test matrices and baseline validation summaries.
* 📁 **`troubleshooting/`** - Log files documenting the engineered staff-port VLAN mismatch and restoration process.
* 📁 **`milestones/milestone-3/`** - The final full-length technical engineering report document.

---

## 🛠️ Network Setup & Deployment Instructions
To open and verify this network topology inside Cisco Packet Tracer, follow these steps:

1. **Prerequisites:** Ensure you have **Cisco Packet Tracer (v8.X or higher)** installed.
2. **Download File:** Navigate to the `/packet-tracer/` directory in this repo and download the `.pkt` file.
3. **Link Booting:** Upon opening, wait roughly **30 to 60 seconds** for Spanning Tree Protocol (STP) to transition all interfaces from amber blocking states to green forwarding states. You can use the **Fast Forward Time** button to skip this delay.
4. **Validation:** Open the command prompt on endpoints (`PC-STAFF01`, `CONTRACTOR-LAPTOP`, or `OFF-SITE-ADMIN`) to run test pings across your subinterfaces.

---

## 🔒 Implemented Security Benchmarks
* **Extended ACL Protection:** An access list configured inbound on subinterface `Gig0/0.40` blocks contractor traffic (`10.36.40.0/24`) from accessing internal subnets (`10.36.10.0/24`, `10.36.20.0/24`, `10.36.30.0/24`).
* **CR9 Management Isolation:** SSHv2 is strictly enforced on `line vty 0 4`. An infrastructure access list blocks all remote administrative access attempts unless they explicitly originate from the **OFF-SITE-ADMIN** public IP link (`192.168.100.2`). Telnet is completely disabled.

---
*For a granular look into device running configurations or step-by-step diagnostic records, browse the targeted subdirectories listed in the directory structure matrix above.*
