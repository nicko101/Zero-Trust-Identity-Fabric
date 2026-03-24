# Design Standards: Identity & Policy Orchestration

This document defines the logical standards for the Policy Decision Point (PDP). It outlines the authentication protocols, authorization logic, and cloud-compliance integration required to enforce Zero Trust access.

---

## 1. Authentication Standards (802.1X)
To ensure the highest level of identity assurance, the fabric utilizes Tunneled Extensible Authentication Protocol (TEAP) to provide "Chained Authentication."

* **Primary Protocol:** TEAP (EAP-FAST) with Chained Authentication.
* **Inner Methods:** EAP-TLS for machine-based identity and EAP-MSCHAPv2 for user-based identity.
* **Fall-back:** PEAP (EAP-MSCHAPv2) for legacy endpoints and BYOD.
* **Logic Gate:** Access is only granted if both the machine (certificate) and user (credentials) are verified against the Hybrid Active Directory.

## 2. ClearPass Cluster Design (PDP)
The Aruba ClearPass Policy Manager (CPPM) is deployed as a highly available cluster to ensure continuous enforcement.

* **Cluster Role:** Publisher/Subscriber model.
* **Trust Anchor:** Rooted in the On-Premises Enterprise PKI for RADIUS server certificates.
* **External Providers:** * **AD DS:** For identity lookups and group membership.
  * **Microsoft Graph API:** For real-time endpoint compliance status via the Intune Extension.

## 3. Compliance Integration: Intune Extension v6.4.1
The policy engine is logically linked to Microsoft Intune to perform posture-based enforcement.

* **The Integration Win:** ClearPass queries the Intune Extension during the RADIUS request.
* **Compliance Flags:** Devices marked "Non-Compliant" in Intune (due to disk encryption, OS version, or firewall status) are automatically placed in a Quarantine role.
* **Polling Interval:** Real-time lookup ensures that a device lost or compromised can be blocked at the switch port within seconds of a compliance change.

## 4. Authorization Logic: Downloadable User Roles (DUR)
To eliminate manual VLAN assignment on switches, the fabric utilizes Dynamic Enforcement via Downloadable User Roles (DUR).

| Role Name | VLAN Assignment | Policy Enforcement Point (PEP) |
| :--- | :--- | :--- |
| **EMP_SECURE** | VLAN 20 | Full access to corporate resources + L7 Inspection. |
| **GUEST_IOT** | VLAN 30 | Internet-only access; isolated from Internal Backbone. |
| **QUARANTINE** | VLAN 999 | Restricted to Remediation/Intune/DHCP/DNS only. |

## 5. Security Profile Standards
* **Enforcement:** All DURs fetched from ClearPass are signed with an HTTPS certificate to prevent role-tampering at the switch level.
* **Accounting:** RADIUS Accounting is enabled on all AOS-CX ports to provide real-time visibility into session duration and bandwidth consumption for the SIEM node.

---

**Navigation**
[Back to Identity Index](../README.md) | [Back to Main Architecture](../../README.md)