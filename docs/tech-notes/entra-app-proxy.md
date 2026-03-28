# Deep-Dive: Microsoft Entra Application Proxy (NDES)

## 1. The Challenge: Remote SCEP Enrollment
For a mobile Windows 11 device to enroll for certificates via SCEP, it must reach the **NDES server**. Traditionally, this required a VPN or exposing internal ports (80/443) to the public internet.

## 2. The Solution: Entra App Proxy
We utilize the **Microsoft Entra Private Network Connector** to bridge the gap between the cloud and the on-premises lab.
* **Internal URL:** `https://ndes02.lab02.local/`
* **External URL:** `https://ndesproxy-nfcloudlab.msappproxy.net/`

## 3. Security Benefits
* **No Inbound Firewall Rules:** The connector initiates an outbound connection to Azure over 443. 
* **Pre-Authentication:** Azure can enforce Conditional Access (MFA or Device Compliance) before the request is allowed to reach the internal NDES server.
* **Attack Surface Reduction:** By utilizing the Entra Private Network Connector, the NDES server remains "dark" to the public internet, mitigating the risk of direct exploits against the PKI infrastructure.

---

**Navigation**

[Back to Engineering Analysis](../engineering-analysis.md) | [Back to Main Architecture](../../README.md)