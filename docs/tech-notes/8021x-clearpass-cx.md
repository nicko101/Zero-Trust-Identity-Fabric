# Deep-Dive: Identity Edge & ClearPass Integration

## 1. Authentication Framework (TEAP)
This lab uses **Tunnel EAP (TEAP)** to wrap both identities inside a single protected tunnel.
* **Outer Tunnel:** Established via the SCEP-issued Machine Certificate (EAP-TLS).
* **Inner Method:** User authentication via EAP-MSCHAPv2.

## 2. Dynamic Authorization (The Intune API)
ClearPass queries the **Microsoft Graph API** during the authorization phase to check real-time telemetry.
* **Attribute:** `Intune_Compliance_Status`
* **Result:** If the device is marked "Non-Compliant" (e.g., outdated OS or disabled firewall), ClearPass pushes a "Quarantine" role instead of "Production."

## 3. Downloadable User Roles (DUR)
The **Aruba CX 6300** switch downloads the security policy dynamically from ClearPass upon RADIUS Accept.
* **Benefit:** Centralized management. Updating a role in ClearPass automatically applies it to the entire switch fabric without manual port-level configuration.

---
**Navigation**
[Back to Engineering Analysis](../engineering-analysis.md) | [Back to Main Architecture](../../README.md)