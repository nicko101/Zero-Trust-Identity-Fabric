# Deep-Dive: Automated PKI & SCEP Lifecycle

## 1. The Trust Architecture
To achieve Zero-Touch Provisioning (ZTP), this lab utilizes **Simple Certificate Enrollment Protocol (SCEP)**, removing the need for manual CSR generation on endpoints.

### Core Components
* **Microsoft Intune:** Sets the SCEP Policy.
* **NDES:** The on-premises role communicating with the Certificate Authority (CA).
* **Entra Application Proxy:** Securely publishes the internal NDES URL to the internet for remote enrollment.

## 2. The Enrollment Handshake
1. **Profile Deployment:** Intune pushes a SCEP profile (Challenge Password + CA Root) to the Windows device.
2. **Challenge Verification:** The device contacts the NDES Proxy URL. NDES validates the challenge with Intune via the Certificate Connector.
3. **Certificate Issuance:** Once verified, the on-premises Issuing CA generates the certificate and NDES delivers it to the device.

## 3. Operational Evidence
* **Event ID 4004:** Confirms the NDES connector successfully verified the request.
* **Certificate Thumbprint:** Matches the Intune "Hardware" inventory, proving identity uniqueness.

---
**Navigation**
[Back to Engineering Analysis](../engineering-analysis.md) | [Back to Main Architecture](../../README.md)