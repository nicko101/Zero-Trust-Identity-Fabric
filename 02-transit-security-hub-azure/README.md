# Design Standards: Identity Policy and Compliance Engine

## Table of Contents
* [1. Automated Identity Lifecycle (SCEP/NDES)](#1-automated-identity-lifecycle-scepndes)
* [2. Network Access Control (NAC) Logic](#2-network-access-control-nac-logic)
* [3. Aruba AOS-CX Enforcement (DUR)](#3-aruba-aos-cx-enforcement-dur)
* [4. Compliance Posture Standards](#4-compliance-posture-standards)
* [5. Access Validation-Proof Hub](#5-access-validation-proof-hub)

---

## 1. Automated Identity Lifecycle (SCEP/NDES)
Machine identities are distributed via SCEP to all Intune-managed endpoints, eliminating manual credential management.

| Component | Standard | Role |
| :--- | :--- | :--- |
| **NDES Service** | MSCEP.dll | The SCEP listener handling requests for the Issuing CA. |
| **App Proxy Connector** | Azure Private Network | Provides an outbound-only HTTPS conduit for NDES. |
| **Intune Profile** | SCEP Configuration | Deploys Root trust and SCEP settings to endpoints. |

---

## 2. Network Access Control (NAC) Logic
**Aruba ClearPass (CPPM)** acts as the Policy Decision Point (PDP), orchestrating the handshake between the physical network and the cloud identity state.

1. **Identity Verification:** ClearPass validates the client certificate against the internal Issuing CA.
2. **Compliance Handshake:** ClearPass utilizes the **Intune Extension** to query the **Microsoft Graph API** for the device's compliance state.
3. **Authorization:** If the device is "Compliant," ClearPass calculates the appropriate enforcement profile.

---

## 3. Aruba AOS-CX Enforcement (DUR)
The **Aruba AOS-CX Switch** acts as the Policy Enforcement Point (PEP). Upon successful RADIUS authorization, the switch dynamically pulls security instructions from ClearPass.

* **Downloadable User Role (DUR):** Instead of static port configurations, the switch downloads the User Role via HTTPS.
* **Dynamic Assignment:** The DUR specifies the **VLAN ID**, **Inbound/Outbound ACLs**, and **Class of Service (CoS)** for the authenticated session.
* **Centralized Management:** This ensures that security policy is consistent across the entire switching fabric, regardless of the physical port location.

---

## 4. Compliance Posture Standards
Devices are only granted "Trusted" access if they meet the following mandated Intune criteria:
* **Encryption:** BitLocker active on all volumes.
* **Firewall:** Windows Defender enabled for all profiles.
* **Antivirus:** Real-time protection and up-to-date definitions.

---

## 5. Access Validation-Proof Hub
Validation evidence and configuration exports for this service are centralized in the module-level hub. 

### Cloud Networking: Evidence & Audit
* **ClearPass Access Tracker:** Logs showing the "DUR-PUSH" status.
* **AOS-CX CLI Output:** `show port-access clients` showing the assigned role.
* **Intune Portal:** Compliance reports for test endpoints.

---

**Navigation**
[Back to Parent Category](../) | [Back to Main Architecture](../../README.md)