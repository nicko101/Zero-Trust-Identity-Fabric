# Design Standards: Infrastructure Core

## Table of Contents
* [1. Virtualization & Orchestration](#1-virtualization--orchestration)
* [2. On-Premises IP Schema](#2-on-premises-ip-schema-100008)
* [3. Identity & PKI Backbone](#3-identity--pki-backbone)
* [4. Hybrid Connector Services](#4-hybrid-connector-services)
* [5. Naming Convention](#5-naming-convention)

---

## 1. Virtualization & Orchestration
The environment is hosted on a high-availability **Proxmox VE Cluster**.

* **Native Hypervisor Execution:** Core appliances (CPPM, Palo Alto VM-Series) run as native VMs for performance.
* **Multi-Vendor Integration (GNS3):** A nested GNS3 engine simulates Cisco fabric (e.g., CSR 1000v) for cross-vendor RADIUS/TACACS+ validation.

---

## 2. On-Premises IP Schema (10.0.0.0/8)
Critical services are consolidated into a **Shared Services Subnet**.

| VLAN | Name | Subnet | Purpose |
| :--- | :--- | :--- | :--- |
| **5** | Shared Services | 10.0.5.0/24 | ADDS, DNS, Entra Connect, PKI, NDES, App Proxy |
| **10** | Management | 10.0.10.0/24 | Hypervisors, Switches, Out-of-Band Mgt |
| **11** | Wired Clients | 10.0.11.0/24 | 802.1X Authenticated Endpoints |
| **60** | CPPM Cluster | 10.0.60.0/24 | Aruba ClearPass Policy Decision Points |

---

## 3. Identity & PKI Backbone
* **Active Directory:** Windows Server 2022 Hybrid Domain.
* **PKI Hierarchy:** Two-tier design with an **Offline Root CA** and an **Online Issuing CA** for SCEP/NDES distribution.

---

## 4. Hybrid Connector Services
Deployed on the **10.0.5.x** network:
* **Entra Connect:** Identity synchronization.
* **Azure App Proxy Connector:** Outbound-only HTTPS conduit for NDES.
* **Intune Certificate Connector:** CA-to-Intune communication.

---

## 5. Naming Convention
| Environment | Prefix | Example |
| :--- | :--- | :--- |
| **Azure (Cloud)** | `nf-` | `nf-rg1` |
| **On-Prem (DC)** | `onprem-` | `onprem-adds-01` |

---

**Navigation**
[Back to Parent Category](../) | [Back to Main Architecture](../../README.md)