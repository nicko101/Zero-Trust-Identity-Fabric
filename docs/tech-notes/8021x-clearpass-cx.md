# Identity Edge: 802.1X, DUR, and ClearPass Integration

This document details the Policy Enforcement Point (PEP) logic used to secure the wired edge, ensuring that only compliant, identity-verified devices can access the internal fabric.

## 1. The 802.1X Handshake (TEAP)
The environment utilizes **Tunnelled Extensible Authentication Protocol (TEAP)** to allow for Chained Authentication. This ensures that both the **Machine** and the **User** must be authenticated before access is granted.

* **EAP-TLS:** Certificates delivered via the SCEP/NDES lifecycle are used for the primary authentication mechanism.
* **The Logic:** By requiring both machine and user certificates, we prevent unauthorized personal devices from joining the network, even if user credentials are compromised.

## 2. ClearPass Policy Decision Point (PDP)
Aruba ClearPass acts as the central brain for all access decisions. It evaluates multiple data points before returning an enforcement profile.

### Service Logic:
1. **Authentication:** ClearPass validates the certificate against the On-Prem Issuing CA CRL (Certificate Revocation List).
2. **Authorization (Intune Extension):** ClearPass performs an API call to the Microsoft Graph API (via the Intune Extension) to verify the device's "Compliant" status in the cloud.
3. **Identity Mapping:** ClearPass checks Active Directory to confirm the user/machine accounts are still enabled and within the correct Security Groups.

## 3. Downloadable User Roles (DUR)
Rather than using static VLAN assignments on switch ports, this architecture utilizes **Downloadable User Roles (DUR)**.

* **The Enforcement:** Once ClearPass approves the session, it sends a RADIUS VSA (Vendor Specific Attribute) to the ArubaOS-CX switch.
* **The Role:** The switch dynamically downloads the role definition, which includes:
    * The VLAN ID.
    * Layer 4 Access Control Lists (ACLs).
    * Captive Portal redirection (if the device is non-compliant).
* **Benefit:** This allows for "Colorless Ports"—a user can plug into any physical port on the CX switch and receive their specific security policy based on their identity, not their location.

## 4. RADIUS Change of Authorization (CoA)
If a device's compliance status changes in Intune (e.g., it is marked non-compliant), ClearPass can trigger a **CoA (Change of Authorization)**.
* **Action:** The switch is instructed to immediately terminate the session or move the device to a "Quarantine" VLAN without the user needing to unplug.

---

[Back to Engineering Analysis](../engineering-analysis.md)