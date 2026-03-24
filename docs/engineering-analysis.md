# Advanced Network Engineering and Security Integration Analysis

## Executive Summary
This document synthesizes the technical architecture and operational results of an advanced network engineering lab focused on hybrid security, automated certificate management, and dynamic network access control. 

### Core Takeaways
* **Automated Trust:** SCEP via NDES for zero-touch user/machine certificate deployment.
* **Compliance-Based Access:** Real-time ClearPass and Microsoft Intune posture validation.
* **Hybrid Fabric:** Bridging on-premises AD/PKI with Azure via dynamic BGP routing.
* **L7 Visibility:** End-to-end logging across Palo Alto (L7/User-ID) and ClearPass Access Tracker.

---

## 🛠️ Infrastructure Technical Deep-Dives
* **[Proxmox Networking & VyOS NAT Logic](./tech-notes/proxmox-networking.md)**
* **[PKI Lifecycle: SCEP, NDES, and Intune](./tech-notes/pki-scep-lifecycle.md)**
* **[Hybrid Transit: Palo Alto to Azure S2S Handshake](./tech-notes/palo-azure-transit.md)**
* **[Identity Edge: 802.1X, DUR, and ClearPass](./tech-notes/8021x-clearpass-cx.md)**

---

**Navigation**
[Back to Main Architecture](../README.md)
