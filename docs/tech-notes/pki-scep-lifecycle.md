# PKI Lifecycle: SCEP, NDES, and Intune Integration
## Automated Identity Trust and Hybrid-to-Cloud Migration

This document details the automated certificate lifecycle used to establish machine and user identity within the fabric without manual intervention or inbound firewall exposure.

---

## 1. The Trust Chain
The environment utilizes a two-tier hierarchy to ensure the security of the Root:
* **Offline Root CA:** A standalone Root CA used only to issue the Subordinate certificate. It remains powered off to protect the cryptographic foundation of the lab.
* **Enterprise Issuing CA:** A domain-joined Windows Server 2022 that handles all active certificate requests and CRL (Certificate Revocation List) distribution.

## 2. Automated Enrollment (SCEP/NDES)
To bridge the gap between **Microsoft Intune** (Cloud) and the **Issuing CA** (On-Premises), the **Network Device Enrollment Service (NDES)** acts as the registration authority.

### The Secure Request Flow:
1. **Intune Policy:** A SCEP configuration profile is assigned to an endpoint (Windows/iOS/Android).
2. **Challenge Delivery:** Intune provides the device with a unique SCEP challenge password.
3. **App Proxy Tunnel:** The device presents the challenge to the NDES URL. This traffic is routed through the **Entra Private Network Connector**, allowing the NDES server to remain completely internal.
4. **Validation:** The **Intune Certificate Connector** validates the challenge with the Intune service before allowing the CA to sign the request.
5. **Issuance:** The Issuing CA generates the certificate and delivers it to the device for use in **802.1X/TEAP** authentication.

## 3. The "No-Inbound" Security Win: Entra App Proxy
The use of the **Entra Private Network Connector** is a critical component of this architecture. It establishes an outbound-only connection to Azure, creating a secure tunnel for SCEP traffic.
* **Security Benefit:** We avoid the "DMZ Gap" by not exposing a web-facing NDES server. All pre-authentication and traffic scrubbing happen at the Azure edge before hitting the internal PKI.

## 4. Hybrid-to-Cloud Migration Path
The PKI serves as the primary anchor of trust during the transition from legacy Active Directory to modern Cloud management:
* **The Bridge:** As devices are enrolled in Intune via the **Intune Extension**, the PKI ensures they maintain a valid identity that ClearPass recognizes.
* **Continuity:** This allows a device to be managed by the cloud (Intune) while still authenticating against on-premises hardware (AOS-CX) using a certificate issued by the local CA.

## 5. Lifecycle Management
* **Auto-Renewal:** Intune manages the certificate renewal window, automatically triggering a new SCEP handshake before expiration.
* **Revocation:** If a device is marked non-compliant in Intune, the Certificate Connector triggers a revocation on the on-premises CA, updating the CRL and blocking access at the switch port or Palo Alto firewall.

---

[Back to Engineering Analysis](../engineering-analysis.md) | [Back to Main Architecture](../../README.md)