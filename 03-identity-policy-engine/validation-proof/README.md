# Validation Proof: Identity and Policy Engine

This folder contains the engineering blueprints and technical evidence validating the Zero Trust Network Access (ZTNA) policy engine.

It demonstrates how identity, device compliance, and cryptographic trust are translated into dynamic network authorization.

---

## Validation Objectives

This section validates that the Identity and Policy Engine enforces the core Zero Trust principles:

- **Strong Identity Assurance:** Machine and user identities are verified using TEAP (EAP-TLS + EAP-MSCHAPv2)  
- **Device Compliance Enforcement:** Access decisions are based on Microsoft Intune compliance state  
- **Secure Certificate Lifecycle:** Certificates are provisioned and validated without inbound exposure  
- **Policy-Based Authorization:** Access is dynamically enforced via ClearPass DUR roles  
- **End-to-End Identity Flow:** Authentication, validation, and enforcement operate as a single integrated system  

Each validation artefact below supports one or more of these guarantees.

---

## 1. ZTNA Macro-Architecture

High-level blueprints illustrating the full identity lifecycle across endpoints, cloud services, and on-premises infrastructure.

* **ZTNA Master Blueprint:**  
![Master Blueprint](./images/ztnaarchitecture1.png)

* **Hybrid-Cloud Engineering Dossier:**  
![Routing Dossier](./images/ztnaarchitecture2.png)

* **Identity Synchronization Foundation:**  
![Entra Connect Sync](./images/ztnaarchitecture3.png)

---

## 2. Identity and PKI Automation

Validates the secure certificate delivery pipeline and outbound-only communication model.

* **Secure Outbound Tunneling (App Proxy):**  
![App Proxy Flow](./images/ztnaarchitecture4.png)

* **App Proxy Telemetry:**  
![App Proxy Logs](./images/ztnaarchitecture5.png)

* **NDES Cryptographic Mapping:**  
![NDES Registry](./images/ztnaarchitecture6.png)

* **Intune Certificate Connector Status:**  
![Connector Health](./images/ztnaarchitecture7.png)

---

## 3. NAC and Intune Integration (ClearPass + Graph API)

Validates that ClearPass uses Microsoft Intune as the device compliance authority during authentication.

* **Device Identity Automation (SCEP):**  
![SCEP Profiles](./images/ztnaarchitecture8.png)

* **End-to-End Certificate Delivery:**  
![Cert Delivery Validation](./images/ztnaarchitecture9.png)

* **ClearPass Intune Extension Configuration:**  
![Intune Extension](./images/ztnaarchitecture10.png)

* **Compliance Attribute Mapping:**  
![Compliance Dictionary](./images/ztnaarchitecture11.png)

---

## 4. Endpoint Authentication and Compliance State

Validates endpoint readiness and the chained authentication process used to establish identity and compliance.

* **TEAP Authentication Model:**  
![TEAP Rationale](./images/ztnaarchitecture12.png)

* **Endpoint Supplicant Configuration:**  
![TEAP Implementation](./images/ztnaarchitecture13.png)

* **Device Compliance Validation:**  
![Compliance Verification](./images/ztnaarchitecture14.png)

---

## 5. Final Enforcement and Authorization

Demonstrates the transition from unauthenticated access to dynamically enforced network policy.

* **Zero Trust Lifecycle (End-to-End):**  
![ZTNA Synthesis](./images/ztnaarchitecture15.png)

* **Switch Port Enforcement:**  
![Switch Port Success](./images/port-access-clients.png)

* **ClearPass Access Tracker (Authentication Success):**  
![ClearPass Accept](./images/cppm_service_teap.png)

---

## Validation Summary

The results confirm that:

- Identity is verified using chained authentication (TEAP)  
- Device compliance is enforced via Microsoft Intune integration  
- Certificates are securely provisioned using an outbound-only model  
- Access decisions are dynamically enforced through ClearPass policies  
- The full identity lifecycle operates consistently across hybrid infrastructure  

This validates the solution as a fully functional Zero Trust identity and access control architecture.

---

## Navigation

[Back to Identity Index](../README.md) | [Back to Main Architecture](../../README.md)