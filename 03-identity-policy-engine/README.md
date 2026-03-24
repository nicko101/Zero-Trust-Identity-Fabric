# Identity Policy and Compliance Engine
## Policy Decision Point (PDP) Orchestration

This module defines the identity and compliance logic for the fabric. It orchestrates the handshake between the Aruba ClearPass Policy Manager (CPPM) and the Microsoft Intune Graph API to enforce Zero Trust access at the network edge.

---

## 1. Module Structure
* **[Design Standards](./design-standards/)**: Logic for 802.1X (TEAP/PEAP) services, DUR attributes, and Intune Extension parameters.
* **[Implementation Logic](./implementation-logic/)**: ClearPass service configurations and the Microsoft Entra ID bridge.
* **[Validation & Evidence](./validation-proof/)**: RADIUS Access-Accept logs and Intune compliance-check telemetry.

---

## 2. Engineering Deep-Dives
For a detailed technical breakdown of the identity lifecycle and trust automation used in this module, refer to the following engineering notes:

* **[PKI Lifecycle: SCEP, NDES, and Intune Integration](../docs/tech-notes/pki-scep-lifecycle.md)**: An analysis of the automated certificate delivery without inbound firewall exposure.
* **[Identity Federation & Azure App Proxy](../docs/tech-notes/app-proxy-logic.md)**: (Placeholder) Technical analysis of the outbound-only connector logic.

---

## 3. Evidence & Audit
Validation evidence and configuration exports for this service are centralized in the module-level hub. This includes the logic for dynamic role assignment based on endpoint compliance.

**[Access Validation-Proof Hub](./validation-proof/README.md)**

---

## 4. Technical Overview
The Policy Engine follows a strict Zero Trust request flow:
1. **Request:** A device initiates a TEAP connection via an ArubaOS-CX switch.
2. **Identity Verification:** ClearPass authenticates the user/machine against the Hybrid Active Directory.
3. **Compliance Check:** ClearPass utilizes the Intune Extension to query the Microsoft Graph API for the device's real-time compliance status.
4. **Authorization:** Upon validation, ClearPass pushes a Downloadable User Role (DUR) to the switch, automatically assigning the port to **VLAN 11 (Wired Clients)**.

---

[Back to Engineering Analysis](../engineering-analysis.md) | [Back to Main Architecture](../../README.md)