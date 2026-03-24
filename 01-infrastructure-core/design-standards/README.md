# Design Standards: Infrastructure Core

This section defines the foundational hardware, virtualization, and base-operating system standards for the environment.

---

## 1. Virtualization & Orchestration
The environment is hosted on a high-availability **Proxmox VE Cluster**, providing the compute and isolation required for a production-grade security lab.

* **Native Hypervisor Execution:** Core identity and security appliances, including **Aruba ClearPass (CPPM)** and **Palo Alto VM-Series**, run as native VMs for maximum performance.
* **Multi-Vendor Integration (GNS3):** To support complex multi-vendor scenarios, a nested GNS3 engine is utilized. This allows the CPPM cluster to integrate with a simulated switching fabric for cross-vendor RADIUS/TACACS+ automation.

---

## 2. On-Premises IP Schema (10.0.0.0/8)
The following subnets are terminated on the Palo Alto VM-Series via the `ethernet1/2` 802.1Q trunk. Address space is segmented to streamline security inspection and cross-premise synchronization.

| VLAN | Security Zone | Subnet | Purpose / Comment |
| :--- | :--- | :--- | :--- |
| **5** | DC | 10.0.5.0/24 | ADDS, DNS, Entra Connect, PKI, NDES |
| **10** | LAN-MGMT | 10.0.10.0/24 | Hypervisor & Network Infrastructure Mgmt |
| **11** | Wired-LAN-V11 | 10.0.11.0/24 | 802.1X Authenticated Wired Clients |
| **20** | CPPM-DOWN | 10.0.20.0/24 | Critical fallback/CPPM-Down segment |
| **50** | GUEST-WIFI | 10.0.50.0/24 | Guest Wireless Access (Captive Portal) |
| **60** | CPPM | 10.0.60.0/24 | Aruba ClearPass Cluster |
| **70** | QRADAR | 10.0.70.0/24 | SIEM / Security Monitoring Traffic |
| **100** | CORP-WIFI | 10.0.100.0/24 | Corporate WiFi (802.1X EAP-TLS) |
| **200** | WiFi-MGMT | 10.0.200.0/24 | Wireless AP & Controller Infrastructure |

---

## 3. Multi-VR Architecture (Virtual Routers)
To ensure strict isolation between production and guest traffic, the firewall utilizes separate Virtual Routers:
* **Default VR:** Manages standard production, management, and security zones.
* **Guest-VR:** Manages the `GUEST-UNTRUST` (`10.1.1.2/30`) and `GUEST-TRUST` (`10.0.150.10/24`) zones, ensuring a completely separate routing table for non-corporate traffic.

---

## 4. Identity & PKI Backbone
* **Active Directory:** `lab02.local` (Windows Server 2022 Hybrid Domain).
* **PKI Hierarchy:** Two-tier design consisting of an **Offline Root CA** and an **Online Issuing CA** for automated SCEP/NDES certificate distribution.

---

## 5. Hybrid Connector Services
The following "Bridge" servers are deployed on the **10.0.5.x** network to facilitate the secure handshake between on-premises infrastructure and Microsoft Intune/Entra ID:

* **Entra Connect:** Handles identity synchronization to Entra ID.
* **Azure App Proxy Connector:** Provides an outbound-only HTTPS conduit for NDES.
* **Intune Certificate Connector:** Manages communication between the Issuing CA and Microsoft Intune.
* **NDES Server:** The SCEP listener handling certificate requests for managed endpoints.

---

**Navigation**
[Back to Engineering Analysis](../../docs/engineering-analysis.md) | [Back to Main Architecture](../../README.md)