$fullAnalysisContent = @'
# Advanced Network Engineering and Security Integration Analysis

## Executive Summary
This briefing document synthesizes the technical architecture and operational results of an advanced network engineering lab focused on hybrid security, automated certificate management, and dynamic network access control. The core of the environment leverages Microsoft Intune, Aruba ClearPass, Palo Alto Networks (PA-VM), and Microsoft Entra to create a seamless, identity-driven security posture.

### Core Takeaways
* **Automated Trust:** Successful implementation of SCEP via NDES allows for zero-touch deployment of unique user and machine certificates.
* **Compliance-Based Access:** Integration between Aruba ClearPass and Microsoft Intune ensures that network access is granted only to devices verified as "compliant" in real-time.
* **Hybrid Infrastructure:** The environment effectively bridges on-premises resources (Active Directory, Enterprise PKI) with cloud services (Azure VPN, Entra Private Network Connectors) using dynamic routing.
* **Granular Visibility:** Comprehensive logging across Palo Alto firewalls and ClearPass Access Tracker provides end-to-end visibility into flows and traffic patterns.

---

## I. Automated Certificate Management (SCEP & NDES)
* **SCEP and Device Configuration:** Automated trust is established by deploying certificate profiles (Root, Machine, and User) to Windows endpoints via Intune.
* **Certificate Connector:** The Microsoft Intune Certificate Connector facilitates the link between the cloud and the on-premises Issuing CA.
* **Verification:** System logs (Event ID 4004) confirm successful SCEP request processing.

---

## II. Network Access Control and Endpoint Compliance
* **Authentication Framework (TEAP):** Utilizes Tunnel EAP to support dual-method authentication (EAP-TLS for machines and EAP-MSCHAPv2 for users).
* **Intune & ClearPass API:** ClearPass queries the Microsoft Graph API to verify device compliance state before granting "Production" access.
* **Dynamic Enforcement:** Devices meeting compliance standards are assigned specific roles like `LAB02-INTUNE-DEVICE`.

---

## III. Hybrid Cloud and Virtualized Infrastructure
* **Azure VPN:** Uses dynamic BGP routing to connect on-premises environments to Azure.
* **Entra Application Proxy:** Enables secure remote access to internal applications like NDES without inbound firewall holes.
* **Virtualization:** The environment supports a diverse range of virtualized operating systems (Linux, Windows) with consistent DNS and User-ID mapping.

---

## IV. Network Security and Traffic Engineering
* **Palo Alto Integration:** Acts as a central "Router-on-a-stick" with sub-interfaces for VLAN segmentation and DHCP services.
* **User-ID Mapping:** Maps traffic to specific users via XML-API data sources for identity-based policy enforcement.
* **Split Routing:** Directs corporate traffic through the VPN tunnel while allowing public traffic via the local gateway.
* **Guest WiFi:** A dedicated two-phase foundation for guest access using VLAN isolation and a ClearPass self-registration portal.

---

## V. Technical Proofs and Operational Status
| Metric / Entity | Observed Status / Value |
| :--- | :--- |
| **Intune Compliance** | Compliant (Verified) |
| **Azure VPN Status** | Connected (BGP) |
| **Auth Method** | TEAP (EAP-TLS / MSCHAPv2) |
| **NDES SCEP Verification** | Success (Event ID 4004) |
| **Access Device** | AOS-CX-01 (10.0.10.3) |

---

## 🛠️ Infrastructure Technical Deep-Dives
*Detailed technical breakdowns for each core component:*

* **[Proxmox Networking & VyOS NAT Logic](./tech-notes/proxmox-networking.md)**
* **[PKI Lifecycle: SCEP, NDES, and Intune](./tech-notes/pki-scep-lifecycle.md)**
* **[Hybrid Transit: Palo Alto to Azure S2S Handshake](./tech-notes/palo-azure-transit.md)**
* **[Identity Edge: 802.1X, DUR, and ClearPass](./tech-notes/8021x-clearpass-cx.md)**
* **[Palo Alto Security & User-ID Integration](./tech-notes/palo-alto-security.md)**
* **[Secure Guest WiFi Anatomy](./tech-notes/guest-wifi-anatomy.md)**
* **[Entra App Proxy for NDES](./tech-notes/entra-app-proxy.md)**

---

**Navigation**
[Back to Main Architecture](../README.md)
'@

$fullAnalysisContent | Out-File -FilePath ".\docs\engineering-analysis.md" -Encoding utf8 -Force