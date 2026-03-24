# Palo Alto Security and User-ID Integration

This document details the Layer 7 security enforcement and identity-aware policies used to secure the hybrid fabric.

## 1. User-ID and Identity Mapping
To move beyond simple IP-based rules, the firewall utilizes **User-ID** to associate network traffic with specific Active Directory identities.
* **Mechanism:** The firewall parses Active Directory Security Event Logs to map source IPs to usernames (e.g., `lab\nick.f`).
* **Enforcement:** This allows security rules to be scoped to specific AD Groups, ensuring that high-privilege access is restricted to verified identities regardless of their physical location in the lab.

## 2. SSL/TLS Decryption (Forward Proxy)
To prevent threats from hiding inside encrypted streams, the Palo Alto is configured for **SSL Decryption**.
* **PKI Integration:** The firewall utilizes a Subordinate CA certificate issued by the on-premises Microsoft PKI.
* **Inspection:** Because the Issuing CA is trusted by all domain-joined endpoints, the firewall can inspect outbound HTTPS traffic for malicious payloads or unauthorized data exfiltration without causing certificate errors.

## 3. Remote Access: GlobalProtect & Azure SAML
Secure remote connectivity is provided through **GlobalProtect** VPN, utilizing a modern identity-first approach.
* **SAML 2.0 Integration:** Authentication is offloaded to **Azure Entra ID** via a dedicated Enterprise Application. This enables Single Sign-On (SSO) and ensures that GlobalProtect sessions are subject to Azure Conditional Access Policies (MFA).
* **Identity Flow:** Entra ID validates the user, and the Palo Alto receives a SAML assertion to grant access. This provides the firewall with real-time User-ID data for remote clients, ensuring consistent policy enforcement.

## 4. Threat Prevention and Offensive Validation
Every security rule is hardened with **Threat Prevention** profiles to block known vulnerabilities, malware, and spyware.

* **Sandboxing:** Suspicious files are automatically submitted to the **WildFire** cloud for analysis.
* **Offensive Validation (Kali Linux):** The efficacy of these security profiles is validated using an internal **Kali Linux** instance. By executing simulated exploits, credential harvesting, and port scans, the Palo Alto's ability to detect, log, and drop malicious traffic is confirmed and documented.

---

[Back to Engineering Analysis](../engineering-analysis.md)