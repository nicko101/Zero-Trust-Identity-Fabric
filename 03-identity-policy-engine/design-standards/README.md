# Design Standards: Identity and Policy Orchestration

This document defines the logical standards for the Policy Decision Point (PDP). It outlines the authentication protocols, authorization logic, and cloud-based compliance integration required to enforce Zero Trust access across the fabric.

---

## 1. Authentication Standards (802.1X)

To ensure a high level of identity assurance, the fabric utilizes Tunneled Extensible Authentication Protocol (TEAP) with chained authentication.

- **Primary Protocol:** TEAP (EAP-FAST) with chained authentication  
- **Inner Methods:**  
  - EAP-TLS for machine identity (certificate-based)  
  - EAP-MSCHAPv2 for user identity (credential-based)  
- **Fallback Protocol:** PEAP (EAP-MSCHAPv2) for legacy or non-certificate-managed devices  
- **Logic Gate:** Access is granted only when both machine and user identities are successfully validated against Hybrid Active Directory  

**Deep Dive:**  
[802.1X and ClearPass Design](../../docs/tech-notes/8021x-clearpass-cx.md)

---

## 2. Policy Decision Point (ClearPass Cluster)

Aruba ClearPass Policy Manager (CPPM) is deployed as a high-availability cluster to provide centralized policy enforcement across hybrid environments.

- **Cluster Architecture:** Publisher/Subscriber model for resiliency and local survivability  
- **Trust Anchor:** Rooted in the on-premises Enterprise PKI for RADIUS server certificates  
- **Identity Source (AD DS):** Primary identity lookup and group-based authorization  
- **Identity Source (Microsoft Graph API):** Real-time device compliance validation via the Intune Extension  

**Deep Dive:**  
[ClearPass Advanced Services](../../docs/tech-notes/clearpass-advanced-services.md)

---

## 3. Cloud-Based Device Compliance (Intune Integration)

The policy engine integrates with Microsoft Intune to enforce device compliance as part of the authentication and authorization process.

- **Integration Logic:**  
  ClearPass queries the Intune Extension (v6.4.1), which retrieves device compliance state from Microsoft Graph API before granting access  

- **Authorization Condition:**  
  Only devices marked as **Compliant** in Intune are granted full network access  

- **Dynamic Revocation:**  
  If a device becomes non-compliant, ClearPass issues a RADIUS Change of Authorization (CoA) to immediately re-authenticate or move the client to a restricted segment  

This model replaces traditional NAC posture checks with **cloud-driven compliance signals**.

**Deep Dive:**  
[Entra App Proxy Architecture](../../docs/tech-notes/entra-app-proxy.md)  
[PKI SCEP Lifecycle](../../docs/tech-notes/pki-scep-lifecycle.md)

---

## 4. Dynamic Authorization Logic (DUR)

The fabric uses Dynamic Enforcement via Downloadable User Roles (DUR) to eliminate static VLAN assignments and manual switch configuration.

- **Automatic Role Assignment:**  
  Upon successful authentication, ClearPass pushes a DUR to the AOS-CX switch  

- **Dynamic Segmentation:**  
  Roles define VLAN assignment (e.g. **VLAN 11 – Wired Clients**) and apply ACLs dynamically  

- **Policy Mobility:**  
  Security policy follows the user/device regardless of physical connection point  

**Deep Dive:**  
[Aruba CX Switching Standards](../../docs/tech-notes/aruba-cx-switching.md)

---

## 5. Security and Telemetry Standards

To maintain integrity, visibility, and auditability of identity enforcement:

- **Secure Role Delivery:**  
  DURs are retrieved over HTTPS to ensure integrity between ClearPass and network devices  

- **Accounting and Telemetry:**  
  RADIUS accounting is enabled to provide detailed session visibility for monitoring and forensic analysis  

- **Observability Integration:**  
  Logs and identity events are forwarded to centralized monitoring platforms for continuous validation  

**Deep Dive:**  
[SecOps and Observability](../../docs/tech-notes/secops-siem-observability.md)

---

## 6. Hybrid Identity Traffic Flow

The Policy Decision Point (ClearPass) is hosted in Azure and processes authentication requests originating from on-premises infrastructure.

- RADIUS/PEAP authentication traffic is initiated from on-premises switches and access points  
- Traffic traverses the hybrid connection via VPN Gateway  
- All authentication flows are routed through the NVA layer for inspection before reaching ClearPass  

This ensures that identity traffic is fully integrated into the Zero Trust model and is subject to the same inspection and control mechanisms as application traffic.

[Back to Top](#design-standards-identity-and-policy-orchestration) | [Back to Main Architecture](../../README.md)