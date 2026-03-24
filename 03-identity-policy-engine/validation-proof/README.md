# Validation Proof: Identity and Policy Engine

This folder contains the engineering blueprints and technical logs validating the Zero Trust Network Access (ZTNA) logic. This module represents the policy decision point where cryptographic identity and device posture are translated into network authorization.

---

## 1. ZTNA Macro-Architecture
High-level engineering dossiers illustrating the end-to-end integration of managed endpoints, hybrid cloud bridges, and the on-premises core.

* **ZTNA Master Blueprint:** ![Master Blueprint](./images/ztnaarchitecture1.png)
* **Hybrid-Cloud Engineering Dossier:** ![Routing Dossier](./images/ztnaarchitecture2.png)
* **Identity Synchronization Foundation:** ![Entra Connect Sync](./images/ztnaarchitecture3.png)

---

## 2. Identity and PKI Automation
Validation of the secure outbound tunnel (App Proxy) and the SCEP certificate delivery pipeline.

* **Data Flow: Secure Outbound Tunneling:** ![App Proxy Flow](./images/ztnaarchitecture4.png)
* **Telemetry: App Proxy Execution:** ![App Proxy Logs](./images/ztnaarchitecture5.png)
* **Cryptographic Mapping (NDES Registry):** ![NDES Registry](./images/ztnaarchitecture6.png)
* **Middleware Status: Intune Certificate Connector:** ![Connector Health](./images/ztnaarchitecture7.png)

---

## 3. NAC and MDM Integration (ClearPass and Intune)
Evidence of the policy engine querying the Microsoft Graph API to verify device health before granting access.

* **Automating Device Identity (SCEP Logic):** ![SCEP Profiles](./images/ztnaarchitecture8.png)
* **End-to-End Cryptographic Delivery:** ![Cert Delivery Validation](./images/ztnaarchitecture9.png)
* **Middleware Configuration: ClearPass Intune Extension:** ![Intune Extension](./images/ztnaarchitecture10.png)
* **Data Translation Matrix (Compliance Dictionary):** ![Compliance Dictionary](./images/ztnaarchitecture11.png)

---

## 4. Endpoint Readiness and Compliance
Validation of the client-side state and the chained authentication protocol (TEAP) used to verify both machine and user identity.

* **Authentication Evolution (TEAP Rationale):** ![TEAP Rationale](./images/ztnaarchitecture12.png)
* **Endpoint Supplicant Configuration:** ![TEAP Implementation](./images/ztnaarchitecture13.png)
* **Pre-Authentication Readiness:** ![Compliance Verification](./images/ztnaarchitecture14.png)

---

## 5. Final Synthesis and Enforcement
The successful conclusion of the ZTNA lifecycle, demonstrating the transition from an unauthenticated request to a dynamic enforcement state.

* **Synthesis: The Zero Trust Lifecycle:** ![ZTNA Synthesis](./images/ztnaarchitecture15.png)
* **Edge Enforcement Verification:** ![Switch Port Success](./images/port-access-clients.png)
* **ClearPass Access Tracker Detail:** ![ClearPass Accept](./images/cppm_service_teap.png)

---

## Navigation
[Back to Identity Index](../README.md) | [Back to Main Architecture](../../README.md)