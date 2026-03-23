# Design Standards: Identity & Policy Integration

This section defines the logic and compliance standards for the Zero Trust Identity layer, focusing on automated certificate lifecycles and NAC enforcement.

---

## 1. Automated Identity Lifecycle (SCEP/NDES)
To eliminate manual credential management, machine identities are distributed automatically via the Simple Certificate Enrollment Protocol (SCEP).

| Component | Standard | Role |
| :--- | :--- | :--- |
| **Certificate Template** | LAB02-INTUNE | Defines SAN, Key Usage (Signature/Encryption). |
| **NDES Service** | MSCEP.dll | Translates SCEP requests to the Issuing CA. |
| **App Proxy** | Outbound Conduit | Publishes the NDES URL via Entra Private Network. |
| **Intune Profile** | SCEP Configuration | Deploys SCEP settings and Root CA Trust to endpoints. |

---

## 2. Network Access Control (NAC) Logic
The **Aruba ClearPass (CPPM)** cluster acts as the Policy Decision Point (PDP) for all 802.1X transactions.

### Enforcement Workflow:
1. **Request:** Client presents a machine certificate via TEAP/PEAP to the AOS-CX Switch.
2. **Identity Verification:** ClearPass validates the certificate against the `lab02-INTERCA-CA`.
3. **Compliance Check:** ClearPass queries the **Microsoft Graph API** using the `CPPM-Native-Lookup` App Registration.
4. **Authorization:** If `isCompliant == True`, ClearPass pushes a **Downloadable User Role (DUR)** to the switch.

---

## 3. Compliance Posture Standards
Devices are only granted "Trusted" access if they meet the following Intune Compliance criteria:
* **Encryption:** BitLocker must be active on all data volumes.
* **Firewall:** Windows Defender Firewall must be enabled for all profiles.
* **Antivirus:** Real-time protection and up-to-date definitions required.
* **OS Version:** Minimum build versions enforced to mitigate known vulnerabilities.

---

## 4. API & RBAC Security
Access to the Microsoft Graph API is restricted to the principle of least privilege:
* **Application ID:** `CPPM-Native-Lookup`
* **Permissions:** `Device.Read.All`, `Directory.Read.All` (Required for Compliance & Group lookups).