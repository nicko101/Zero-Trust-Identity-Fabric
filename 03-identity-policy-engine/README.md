# Identity Policy and Compliance Engine  
## Policy Decision Point (PDP) Orchestration

This module defines the identity and compliance logic for the fabric. It orchestrates the handshake between Aruba ClearPass Policy Manager (CPPM) and Microsoft Intune (via Graph API) to enforce Zero Trust access at the network edge.

---

## Module Structure

- **[Design Standards](./design-standards/)**  
  Defines 802.1X (TEAP/PEAP) services, DUR attributes, and Intune Extension integration  

- **[Implementation Logic](./implementation-logic/)**  
  Details ClearPass service configuration and identity flow integration with Entra ID  

- **[Validation & Evidence](./validation-proof/)**  
  Provides RADIUS logs, compliance validation, and role enforcement proof  

---

## Core Identity Flow (Zero Trust PDP)

The Policy Engine follows a strict Zero Trust request flow:

1. **Request Initiation**  
   A device connects to the network and initiates 802.1X (TEAP/PEAP) authentication via an Aruba AOS-CX switch  

2. **Identity Verification**  
   ClearPass authenticates the user and/or device against Hybrid Active Directory  

3. **Compliance Validation**  
   ClearPass queries Microsoft Intune via the Graph API (Intune Extension) to validate real-time device compliance  

4. **Authorization & Enforcement**  
   Upon successful validation, ClearPass returns a Downloadable User Role (DUR), dynamically enforcing access policies (e.g. VLAN assignment, ACLs) at the switch  

This ensures access decisions are based on **identity + device compliance**, not network location.

---

## Advanced Identity Services

In addition to core 802.1X enforcement, the environment includes extended ClearPass capabilities:

- **Guest Access (Captive Portal)**  
  Self-service and sponsored guest onboarding workflows for unmanaged devices  
  → See: [Guest WiFi Anatomy](../docs/tech-notes/guest-wifi-anatomy.md)

- **Onboard (Certificate Provisioning)**  
  Automated device onboarding and certificate deployment for secure EAP-TLS authentication  

- **OnGuard (Endpoint Posture Assessment)**  
  Traditional NAC-based posture checks (e.g. AV status, patch level) for non-Intune-managed devices  

- **Cloud-Based Device Compliance (Intune Integration)**  
  ClearPass queries Microsoft Intune via the Graph API to validate real-time device compliance as part of the authorization decision  

These services extend the PDP into a **full identity lifecycle platform**, covering onboarding, authentication, posture, and enforcement.

---

## Engineering Deep-Dives

For detailed technical breakdowns of supporting components:

- **[PKI Lifecycle: SCEP, NDES, and Intune Integration](../docs/tech-notes/pki-scep-lifecycle.md)**  
  Automated certificate delivery without inbound firewall exposure  

- **[Identity Federation & Azure App Proxy](../docs/tech-notes/app-proxy-logic.md)**  
  Outbound-only secure connector architecture  

- **[ClearPass Advanced Services](../docs/tech-notes/clearpass-advanced-services.md)**  
  Deep dive into DUR, profiling, and service logic  

---

## Evidence & Validation

Validation evidence and configuration outputs for this module are centralized here:

- RADIUS Access-Accept logs  
- Intune compliance validation  
- Dynamic role enforcement proof  

[Access Validation-Proof Hub](./validation-proof/README.md)

---

## Design Outcome

This module establishes:

- Identity as the **primary control plane**  
- Device compliance as a **real-time authorization signal**  
- Dynamic segmentation via **role-based enforcement (DUR)**  
- A fully integrated **cloud-native + on-prem identity model**  

---

[Back to Engineering Analysis](../engineering-analysis.md) | [Back to Main Architecture](../../README.md)