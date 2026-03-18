# Hybrid Secure Network Anatomy
## Full-Stack Enterprise ZTNA and Multi-Tunnel Transit Hub

![Hero Image](./diagrams/Automating%20Zero%20Trust%20Access%20Blueprint.png)

---

### Executive Summary: The Technical Flow
This architecture demonstrates a production-hardened Hybrid Zero Trust environment. It orchestrates a complex handshake between a Hybrid Active Directory Domain and Azure Cloud Services to enforce identity-based access.

#### 1. [The Virtualization Layer (Proxmox and GNS3)](./01-infrastructure-core/)
The entire stack is virtualized on a Proxmox VE hypervisor cluster. A nested GNS3 Server orchestrates high-fidelity network hardware simulation, allowing for hardware-accurate execution of Aruba AOS-CX switching and Aruba ClearPass (CPPM) cluster logic in a controlled, sandboxed environment.

#### 2. [Authentication and Authorization (802.1X and DUR)](./03-identity-policy-engine/)
On-premises clients connect via Aruba AOS-CX switches using 802.1X (TEAP/PEAP) against a Hybrid Domain (Synchronized via Entra Connect). 

* Policy Decision Point (PDP): Authentication is handled by an Aruba ClearPass (CPPM) cluster (On-Prem or Azure VM).
* The Intune Extension: During the RADIUS handshake, ClearPass utilizes the Microsoft Intune Extension (v6.4.1) to query the Microsoft Graph API for real-time compliance status.
* Dynamic Enforcement: Upon validation, ClearPass pushes a Downloadable User Role (DUR) to the switch. This DUR specifies the VLAN ID, while the Palo Alto NVA (acting as the DHCP Relay/Gateway) provides the IP address and stateful security.

#### 3. [The Dual-Tunnel Hybrid Transit Hub](./02-transit-security-hub-azure/)
* The Internal Backbone (Tunnel 200): Connects on-premises infrastructure to the Azure VPN Gateway. Azure Route Tables are configured to force all incoming gateway traffic to the Palo Alto NVA Private IP, ensuring that even internal on-prem-to-spoke traffic undergoes full L7 inspection.
* The Secure Breakout (Tunnel 300): Internet-bound traffic is steered via a direct S2S tunnel to the Palo Alto NVA. This ensures all egress traffic undergoes stateful inspection and Symmetric PBF before breakout.
* Inspection Force-Multiplier: All spoke-to-spoke, spoke-to-internet, and on-prem-to-cloud traffic is centralized through the NVA for a unified security posture.

#### 4. [Automated Identity Lifecycle (SCEP and App Proxy)](./01-infrastructure-core/)
The Entra Private Network Connector (App Proxy) handles outbound-only requests, allowing Microsoft Intune to communicate SCEP payloads to the internal NDES server without inbound firewall exceptions.

---

### Technical Tech Stack
| Layer | Technology / Vendor | Role in the Integration |
| :--- | :--- | :--- |
| Virtualization | Proxmox and GNS3 | System-wide hypervisor and nested network orchestration. |
| Security Hub | Palo Alto VM-Series | NVA, DHCP Server, Symmetric PBF, and Tunnel 300. |
| Policy | Aruba ClearPass | RADIUS/802.1X, DUR delivery, and Intune Extension. |
| Endpoint | Microsoft Intune | SCEP/NDES lifecycle and Compliance Posture. |
| Edge | Aruba AOS-CX | Policy Enforcement Point (PEP) for 802.1X and DUR. |

---

### Detailed Engineering Pillars
* [01. Infrastructure and PKI Hub](./01-infrastructure-core/)
* [02. Transit Security and Routing Hub](./02-transit-security-hub-azure/)
* [03. Identity Policy and Compliance Engine](./03-identity-policy-engine/)
