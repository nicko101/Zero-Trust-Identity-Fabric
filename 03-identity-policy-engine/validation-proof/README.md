# Validation Proof: Identity and Policy Engine

This folder contains the engineering blueprints and technical logs validating the Zero Trust Network Access (ZTNA) logic. This module represents the policy decision point where cryptographic identity and device posture are translated into network authorization.

## Table of Contents
* [1. ZTNA Macro-Architecture](#1-ztna-macro-architecture)
* [2. Identity and PKI Automation](#2-identity-and-pki-automation)
* [3. NAC and MDM Integration (ClearPass and Intune)](#3-nac-and-mdm-integration-clearpass-and-intune)
* [4. Endpoint Readiness and Compliance](#4-endpoint-readiness-and-compliance)
* [5. Final Synthesis and Enforcement](#5-final-synthesis-and-enforcement)

---

## 1. ZTNA Macro-Architecture
High-level engineering dossiers illustrating the end-to-end integration of managed endpoints, hybrid cloud bridges, and the on-premises core.

* **ZTNA Master Blueprint:** ![Master Blueprint](ZTNAArchitecture1.png)
* **Hybrid-Cloud Engineering Dossier:** ![Routing Dossier](ZTNAArchitecture2.png)
* **Identity Synchronization Foundation:** ![Entra Connect Sync](ZTNAArchitecture3.png)

---

## 2. Identity and PKI Automation
Validation of the secure outbound tunnel (App Proxy) and the SCEP certificate delivery pipeline.

* **Data Flow: Secure Outbound Tunneling:** ![App Proxy Flow](ZTNAArchitecture4.png)
* **Telemetry: App Proxy Execution:** ![App Proxy Logs](ZTNAArchitecture5.png)
* **Cryptographic Mapping (NDES Registry):** ![NDES Registry](ZTNAArchitecture6.png)
* **Middleware Status: Intune Certificate Connector:** ![Connector Health](ZTNAArchitecture7.png)

---

## 3. NAC and MDM Integration (ClearPass and Intune)
Evidence of the policy engine querying the Microsoft Graph API to verify device health before granting access.

* **Automating Device Identity (SCEP Logic):** ![SCEP Profiles](ZTNAArchitecture8.png)
* **End-to-End Cryptographic Delivery:** ![Cert Delivery Validation](ZTNAArchitecture9.png)
* **Middleware Configuration: ClearPass Intune Extension:** ![Intune Extension](ZTNAArchitecture10.png)
* **Data Translation Matrix (Compliance Dictionary):** ![Compliance Dictionary](ZTNAArchitecture11.png)

---

## 4. Endpoint Readiness and Compliance
Validation of the client-side state and the chained authentication protocol (TEAP) used to verify both machine and user.

* **Authentication Evolution: Why TEAP?:** ![TEAP Rationale](ZTNAArchitecture12.png)
* **Endpoint Supplicant Configuration:** ![TEAP Implementation](ZTNAArchitecture13.png)
* **Pre-Authentication Readiness:** ![Compliance Verification](ZTNAArchitecture14.png)

---

## 5. Final Synthesis and Enforcement
The successful conclusion of the ZTNA lifecycle, showing the transition from an unauthenticated request to a dynamic "ACCEPT" state.

* **Synthesis: The Zero Trust Lifecycle:** ![ZTNA Synthesis](ZTNAArchitecture15.png)
* **Edge Enforcement Verification:** ![Switch Port Success](port-access-clients.png)
* **ClearPass Access Tracker Detail:** ![ClearPass Accept](cppm_service_teap.png)

---

## Navigation
[Back to Main Architecture](../../README.md)