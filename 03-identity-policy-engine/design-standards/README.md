# Design Standards: Identity and Policy Orchestration

This document defines the logical standards for the Policy Decision Point (PDP). It outlines the authentication protocols, authorization logic, and cloud-compliance integration required to enforce Zero Trust access across the fabric.

## 1. Authentication Standards (802.1X)
To ensure the highest level of identity assurance, the fabric utilizes Tunneled Extensible Authentication Protocol (TEAP) to provide Chained Authentication.

* **Primary Protocol:** TEAP (EAP-FAST) with Chained Authentication.
* **Inner Methods:** EAP-TLS for machine-based identity and EAP-MSCHAPv2 for user-based identity.
* **Fall-back Protocol:** PEAP (EAP-MSCHAPv2) is maintained for legacy endpoints or non-certificate managed devices.
* **Logic Gate:** Access is granted only when both the machine (via certificate) and the user (via credentials) are successfully verified against the Hybrid Active Directory.
* **Deep Dive:** [802.1X and ClearPass Design](../../docs/tech-notes/8021x-clearpass-cx.md)

## 2. Policy Decision Point (ClearPass Cluster)
The Aruba ClearPass Policy Manager (CPPM) is deployed as a high-availability cluster to ensure continuous policy enforcement across the on-premises and cloud segments.

* **Cluster Architecture:** Publisher/Subscriber model for localized survivability.
* **Trust Anchor:** Rooted in the On-Premises Enterprise PKI for RADIUS server certificates.
* **Identity Source (AD DS):** Leveraged for primary identity lookups and group-based authorization.
* **Identity Source (Microsoft Graph API):** Leveraged for real-time endpoint compliance status via the Intune Extension.
* **Deep Dive:** [ClearPass Advanced Services](../../docs/tech-notes/clearpass-advanced-services.md)

## 3. Cloud Compliance Integration (Intune)
The policy engine is logically linked to Microsoft Intune to perform posture-based enforcement during the RADIUS handshake, bridging cloud health with on-premises network access.

* **The Integration Logic:** ClearPass queries the Intune Extension (v6.4.1) to verify the "Compliant" status of a device in the cloud before granting internal network access.
* **Dynamic Revocation:** If a device is marked non-compliant in Intune, ClearPass triggers a RADIUS Change of Authorization (CoA) to immediately bounce the port or move the client to a restricted quarantine segment.
* **Deep Dive:** [Entra App Proxy Architecture](../../docs/tech-notes/entra-app-proxy.md) | [PKI SCEP Lifecycle](../../docs/tech-notes/pki-scep-lifecycle.md)

## 4. Dynamic Authorization Logic (DUR)
The fabric utilizes Dynamic Enforcement via Downloadable User Roles (DUR) to completely eliminate manual switch port configuration and static VLAN assignments.

* **Automatic Assignment:** Upon a successful authentication handshake, the Enforcement Profile automatically pushes a DUR to the AOS-CX switch.
* **Dynamic Segmentation:** This role assigns the switch port to **VLAN 11 (Wired Clients)** and applies necessary ACLs, ensuring that the security policy follows the user regardless of their physical port location.
* **Deep Dive:** [Aruba CX Switching Standards](../../docs/tech-notes/aruba-cx-switching.md)

## 5. Security Profile Standards
To maintain the integrity and observability of the authorization process, strict profile standards are enforced.

* **Secure Enforcement:** All DURs are fetched over HTTPS to ensure role integrity between the ClearPass cluster and the AOS-CX switches.
* **Accounting and Telemetry:** RADIUS Accounting is strictly enabled to provide detailed session telemetry to the centralized observability node for continuous audit and forensics.
* **Deep Dive:** [SecOps and Observability](../../docs/tech-notes/secops-siem-observability.md)

---
[Back to Top](#design-standards-identity-and-policy-orchestration) | [Back to Main Architecture](../../README.md)