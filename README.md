--
# Three-Zone OT Lab Proof of Concept (POC)

## 📖 Overview
This repository contains the technical design blueprint and provisioning roadmap for a **Three-Zone Operational Technology (OT) Lab Proof of Concept**.  
The lab demonstrates defense-in-depth segmentation, secure conduit control, and industrial process simulation using **pfSense**, **OpenPLC**, and **Factory I/O**.

---

## 🏗️ Architecture Layout
The lab is segmented into three distinct zones managed by a centralized **pfSense security gateway**:

| Zone | Subnet | Primary Host | Purpose |
|------|--------|--------------|---------|
| **Zone 1 – IT / Management** | 192.168.10.0/24 | Ubuntu Engineering Workstation (192.168.10.5) | HMI monitoring, analytics, centralized logging |
| **Zone 2 – OT Control** | 10.0.20.0/24 | OpenPLC Runtime Engine (10.0.20.10) | Controller execution layer, automation loops |
| **Zone 3 – Process Simulation** | 172.16.30.0/24 | Factory I/O Simulation Host (172.16.30.20) | Real-time industrial process rendering |

---

## ⚙️ Provisioning Guide

### Step 1: Virtual Networking
- Create three isolated host-only networks:
  - `LAN_IT_ZONE`
  - `LAN_OT_ZONE`
  - `LAN_SIM_ZONE`

### Step 2: pfSense Firewall Setup
- Deploy pfSense VM with 3 NICs bound to each zone.
- Configure static IPs:
  - IT: `192.168.10.1/24`
  - OT: `10.0.20.1/24`
  - SIM: `172.16.30.1/24`

### Step 3: OT Control Node (OpenPLC)
- Deploy Ubuntu Server VM on `LAN_OT_ZONE`.
- Assign static IP `10.0.20.10`.
- Install OpenPLC Runtime:
  ```bash
  git clone https://github.com/thiagoralves/OpenPLC_v3.git
  cd OpenPLC_v3
  ./install.sh linux
  ```
- Verify Modbus port `502` and web console `8080`.

### Step 4: Simulation Node (Factory I/O)
- Deploy Windows VM on `LAN_SIM_ZONE`.
- Configure IP `172.16.30.20`.
- Enable **Modbus TCP Server** on port `502`.

### Step 5: Management Node (Engineering Workstation)
- Deploy Ubuntu Desktop VM on `LAN_IT_ZONE`.
- Configure IP `192.168.10.5`.
- Install diagnostic utilities:
  ```bash
  sudo apt update && sudo apt install snapd -y
  sudo snap install qmodmaster
  ```

---

## 🔒 Firewall Rules (ACLs)
Configured in **pfSense → Firewall → Rules**:

| Source | Destination | Protocol/Port | Action | Purpose |
|--------|-------------|---------------|--------|---------|
| IT (192.168.10.5) → OT (10.0.20.10) | TCP/502 | PASS | SCADA/Modbus polling |
| IT (192.168.10.5) → OT (10.0.20.10) | TCP/8080 | PASS | PLC web console access |
| OT (10.0.20.10) → SIM (172.16.30.20) | TCP/502 | PASS | PLC ↔ Factory I/O communication |
| ANY → ANY | ANY | DENY | Default drop rule |

---

## 📌 Notes
- This lab is **isolated** and should not be connected to production networks.
- Empty folders are not tracked in GitHub. To add lab artifacts, create a folder with a `README.md` or placeholder file inside.

---
