# Advanced Network Engineering and Security Integration Analysis

## Executive Summary
This briefing document synthesizes the technical architecture of an advanced network engineering lab focused on hybrid security, automated certificate management, and dynamic network access control. The environment leverages Microsoft Intune, Aruba ClearPass, Palo Alto Networks, and Microsoft Entra to create a seamless, identity-driven Zero Trust posture.

### Core Takeaways
* **Automated Trust:** SCEP via NDES allows for zero-touch deployment of unique user and machine certificates without inbound firewall exposure.
* **Compliance-Based Access:** Integration between ClearPass and Intune shifts the authorization source of truth from legacy Active Directory to real-time cloud compliance.
* **Hybrid Infrastructure:** The environment bridges on-premises resources with Azure via split-routing architectures, optimizing both performance and inspection.
* **Offensive Validation:** Security policies are actively verified using Kali Linux to confirm protection against real-world threats.

---

## I. Automated Certificate Management (Outbound-Only PKI)
* **The Legacy Problem:** Traditional PKI requires inbound DMZ exposure to issue certificates to remote clients.
* **The Zero Trust Solution:** Azure App Proxy, SCEP, and NDES create a secure, outbound-only tunnel. The internal PKI stays completely isolated, yet remote devices receive trusted certificates automatically.
* **Certificate Connector:** The Microsoft Intune Certificate Connector links the cloud and the on-premises Issuing CA, verifying device management status before issuance.
* **Verification:** System logs (Event ID 4004) confirm successful SCEP processing.

---

## II. Network Access Control: The Cloud-Native Shift
* **The "Vanishing" Domain Controller:** As endpoints migrate to full Entra ID joined status, local Active Directory cannot authorize them. This architecture solves the "management gap" by shifting authorization to the cloud.
* **Intune Compliance as the Gatekeeper:** ClearPass uses the Intune Extension to query the Microsoft Graph API. It verifies real-time device health (OS version, BitLocker) rather than checking a static LDAP account.
* **Authentication Framework (TEAP):** Utilizes Tunnel EAP to support dual-method authentication (EAP-TLS for machines and EAP-MSCHAPv2 for users).
* **Dynamic Enforcement:** Devices meeting Intune compliance standards are assigned specific network access via Downloadable User Roles (DUR) on the ArubaOS-CX fabric.

---

## III. Hybrid Transit Architecture
* **Overcoming the Single-Tunnel Bottleneck:** Terminating VPNs directly on a security appliance limits Active/Active scaling. The enterprise evolution offloads IPsec cryptography to the Azure VPN Gateway, allowing load-balanced scale behind it.
* **BGP & Routing Propagation:** Uses dynamic BGP routing to connect on-premises environments to Azure, ensuring high availability for the management backbone.
* **Virtualization:** The environment supports diverse OS instances on a Proxmox cluster with consistent DNS and User-ID mapping.

---

## IV. Network Security and Split-Routing Logic
* **The Split-Routing Paradigm:** Traffic is dynamically steered via Azure User-Defined Routes (UDRs) to decouple management traffic from security inspection.
    * *Management Path:* System routes handle infrastructure sync (AD, DNS) directly on the Azure backbone.
    * *Security Transit:* UDRs force high-risk/internet-bound traffic from the Azure Spokes back to the Palo Alto NVA for deep Layer 7 inspection.
* **Palo Alto Integration:** Acts as the central gateway with sub-interfaces for segmentation. App-ID and User-ID are enforced on all transit traffic.
* **High Availability (HA):** Configured for stateful session failover and tunnel persistence.

---

## V. Technical Proofs and Operational Status

| Metric / Entity | Observed Status / Value |
| :--- | :--- |
| **Intune Compliance** | Compliant (Verified) |
| **Azure VPN Status** | Connected (BGP) |
| **Auth Method** | TEAP (EAP-TLS / MSCHAPv2) |
| **NDES SCEP Verification** | Success (Event ID 4004) |
| **Switch Orchestration** | Aruba NetEdit Verified |

---

## Infrastructure Technical Deep-Dives
Detailed technical breakdowns for each core component:

* **[Proxmox Networking & VyOS NAT Logic](./tech-notes/proxmox-networking.md)**
* **[Certificate Services: SCEP, NDES, & PKI](./tech-notes/pki-scep-lifecycle.md)**
* **[Hybrid Transit: Palo Alto to Azure S2S Handshake](./tech-notes/palo-azure-transit.md)**
* **[Identity Edge: 802.1X, DUR, & ClearPass](./tech-notes/8021x-clearpass-cx.md)**
* **[Palo Alto L7 Security, HA, & Advanced Routing](./tech-notes/palo-alto-security.md)**
* **[Secure Guest WiFi Anatomy](./tech-notes/guest-wifi-anatomy.md)**
* **[Entra App Proxy for NDES Publishing](./tech-notes/entra-app-proxy.md)**
* **[ArubaOS-CX Switching & NetEdit](./tech-notes/aruba-cx-switching.md)**
* **[ClearPass Advanced: Onboard & OnGuard](./tech-notes/clearpass-advanced-services.md)**
* **[SecOps Observability & SIEM Logging](./tech-notes/secops-siem-observability.md)**
* **[White Hat Pentesting & Offensive Validation](./tech-notes/pentesting-offensive-validation.md)**

---

**Navigation**
[Back to Main Architecture](../README.md)