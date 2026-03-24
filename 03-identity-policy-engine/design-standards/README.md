# Design Standards: Identity & Policy Orchestration

This document defines the logical standards for the Policy Decision Point (PDP). It outlines the authentication protocols, authorization logic, and cloud-compliance integration required to enforce Zero Trust access across the fabric.

---

## 1. Authentication Standards (802.1X)
To ensure the highest level of identity assurance, the fabric utilizes Tunneled Extensible Authentication Protocol (TEAP) to provide Chained Authentication.

* **Primary Protocol:** TEAP (EAP-FAST) with Chained Authentication.
* **Inner Methods:** EAP-TLS for machine-based identity and EAP-MSCHAPv2 for user-based identity.
* **Fall-back:** PEAP (EAP-MSCHAPv2) for legacy endpoints or non-certificate managed devices.
* **Logic Gate:** Access is granted only when the machine (certificate) and user (credentials) are both verified against the Hybrid Active Directory.

## 2. ClearPass Cluster Design (PDP)
The Aruba ClearPass Policy Manager (CPPM) is deployed as a cluster to ensure continuous policy enforcement across the on-premises and cloud segments.

* **Cluster Architecture:** Publisher/Subscriber model.
* **Trust Anchor:** Rooted in the On-Premises Enterprise PKI for RADIUS server certificates.
* **Identity Providers:** * **AD DS:** For identity lookups and group-based authorization.
    * **Microsoft Graph API:** For real-time endpoint compliance status via the Intune Extension.

## 3. Compliance Integration: Intune Extension v6.4.1
The policy engine is logically linked to Microsoft Intune to perform posture-based enforcement during the RADIUS handshake.

* **The Integration Win:** ClearPass queries the Intune Extension (v6.4.1) to check the Compliant status of a device in the cloud before granting internal access.
* **Dynamic Revocation:** If a device is marked non-compliant in Intune, ClearPass can trigger a RADIUS Change of Authorization (CoA) to bounce the port or move the client to a restricted segment.

## 4. Authorization Logic: Downloadable User Roles (DUR)
The fabric utilizes Dynamic Enforcement via Downloadable User Roles (DUR) to eliminate manual port configuration. 

* **Automatic Assignment:** Upon a successful authentication handshake, the **Enforcement Profile** automatically assigns the switch port to **VLAN 11 (Wired Clients)** using a dynamically pushed DUR. This ensures that security policy follows the user regardless of the physical port location.

## 5. Security Profile Standards
* **Enforcement:** All DURs are fetched over HTTPS to ensure role integrity between ClearPass and the AOS-CX switches.
* **Accounting:** RADIUS Accounting is enabled to provide session telemetry to the centralized observability node for audit and forensics.