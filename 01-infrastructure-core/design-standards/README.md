# Design Standards: Infrastructure Core

This section defines the foundational hardware, virtualization, and base-operating system standards for the **Lab02** environment.

---

## 1. Virtualization & Orchestration
The environment is hosted on a high-availability hypervisor cluster, simulating a true production data center.
* **Hypervisor:** Proxmox VE Cluster.
* **Network Simulation:** Nested GNS3 Server for high-fidelity execution of **Aruba AOS-CX** and **ClearPass (CPPM)** virtual appliances.

---

## 2. On-Premises IP Schema (10.0.0.0/8)
The address space is segmented to ensure strict traffic isolation between management, servers, and OT assets.

| VLAN | Name | Subnet | Purpose |
| :--- | :--- | :--- | :--- |
| **10** | Management | 10.10.10.0/24 | Hypervisors, Switches, Out-of-Band Mgt |
| **20** | User Data | 10.20.20.0/24 | Domain-joined workstations |
| **30** | Server Farm | 10.30.30.0/24 | ADDS, PKI, NDES, and App Proxy |
| **40** | OT/ICS | 10.40.40.0/24 | Workcell devices and PLC simulation |

---

## 3. Identity & PKI Backbone
* **Active Directory:** `lab02.local` (Windows Server 2022).
* **PKI Hierarchy:** Two-tier design consisting of an **Offline Root CA** and an **Online Issuing CA** to meet enterprise security standards for SCEP/NDES certificate distribution.

---

## 4. Hybrid Connector Services
To facilitate the secure handshake between on-premises infrastructure and Azure/Intune, the following "Bridge" servers are deployed:

* **Entra Connect:** Handles identity synchronization from `lab02.local` to Entra ID.
* **Azure App Proxy Connector:** Provides an outbound-only HTTPS conduit for NDES, eliminating the need for inbound firewall openings.
* **Intune Certificate Connector:** Manages the communication between the Issuing CA and Microsoft Intune for SCEP certificate issuance.

---

## 5. Naming Convention
All infrastructure resources follow a lowercase, service-centric naming schema to ensure consistency across hybrid environments:
* **Servers:** `[site]-[service]-[index]` (e.g., `onprem-ndes-01`)
* **Network:** `[env]-[resource]-[index]` (e.g., `az-vnet-01`)