# Zero-Trust Identity Fabric
## Full-Stack Enterprise ZTNA and Hybrid Cloud Transit Hub

![Hero Image](./diagrams/Automating%20Zero%20Trust%20Access%20Blueprint.png)

---

## 1. Executive Summary: The Technical Flow
This architecture demonstrates a production-hardened **Hybrid Zero Trust** environment. It orchestrates a high-fidelity handshake between on-premises infrastructure and **Azure Cloud Services** to enforce identity-based access across a distributed fabric.

### ➔ [View Advanced Engineering Analysis](./docs/engineering-analysis.md)

---

## 2. Key Technical Challenges Solved
This project goes beyond standard documentation to solve specific enterprise-grade constraints:

* **NVA Split-Tunnel Routing:** Implementation of specific routing restrictions and Symmetric PBF to manage traffic across dual IPsec tunnels (Backbone vs. Security Breakout), preventing asymmetric routing issues common in NVA deployments.
* **Outbound-Only Cert Retrieval:** Utilizes the **Entra Private Network Connector (App Proxy)** to facilitate internal SCEP/NDES certificate retrieval. This allows Intune to issue identity certs to on-prem devices without requiring inbound firewall holes.
* **Hybrid-to-Cloud Migration Path:** Leverages the **Intune Extension** to bridge the gap between legacy Active Directory and modern Cloud Management, automating the migration of identity trust from the local domain to the Entra ID fabric.
* **Dynamic NAC Orchestration:** Integration of **Aruba ClearPass** and **AOS-CX** to fetch Downloadable User Roles (DUR) over HTTPS, ensuring that security policies are applied at the hardware edge based on real-time compliance data.

---

## 3. Core Architectural Pillars

### Cloud & Hybrid Identity
* **Microsoft Entra ID:** Live tenant configuration and Enterprise App integrations.
* **Microsoft Intune:** MDM enrollment and automated SCEP profile distribution.
* **Hybrid Identity:** Entra Connect synchronization and App Proxy for secure internal resource publishing.

### Network Security & NAC
* **Palo Alto Networks (VM-Series):** Active/Passive HA cluster enforcing App-ID and User-ID.
* **Aruba ClearPass (CPPM):** Policy Decision Point (PDP) managing the full RADIUS/TACACS+ lifecycle.
* **Observability Node:** Dedicated Linux node for **Syslog** aggregation and **SNMP** device profiling.

---

## 4. Detailed Engineering Deep-Dives
* [Proxmox Networking & VyOS NAT Logic](./docs/tech-notes/proxmox-networking.md)
* [PKI Lifecycle: SCEP, NDES, and Intune](./docs/tech-notes/pki-scep-lifecycle.md)
* [Hybrid Transit: Palo Alto to Azure S2S](./docs/tech-notes/palo-azure-transit.md)
* [Identity Edge: 802.1X & DUR Logic](./docs/tech-notes/8021x-clearpass-cx.md)
* [Palo Alto Security & User-ID Integration](./docs/tech-notes/palo-alto-security.md)
* [Secure Guest WiFi Anatomy](./docs/tech-notes/guest-wifi-anatomy.md)
* [Entra App Proxy for NDES](./docs/tech-notes/entra-app-proxy.md)
* [ArubaOS-CX Switching & Port Access](./docs/tech-notes/aruba-cx-switching.md)
* [ClearPass Advanced Services](./docs/tech-notes/clearpass-advanced-services.md)
* [SIEM & SecOps: Centralized Logging](./docs/tech-notes/secops-siem-observability.md)
* [Offensive Validation & Pentesting](./docs/tech-notes/pentesting-offensive-validation.md)