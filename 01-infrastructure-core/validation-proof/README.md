# Validation Proof: Infrastructure Core

This folder contains the technical evidence validating the baseline identity services, PKI hierarchy, and secure connector infrastructure for the hybrid environment.

## Table of Contents
* [1. Identity Synchronization (Hybrid Domain)](#1-identity-synchronization-hybrid-domain)
* [2. SCEP & Intune Certificate Infrastructure](#2-scep--intune-certificate-infrastructure)
* [3. App Proxy & Secure Connector Services](#3-app-proxy--secure-connector-services)
* [4. Network Management & Edge Baseline](#4-network-management--edge-baseline)

---

## 1. Identity Synchronization (Hybrid Domain)
Validation of the bidirectional identity handshake between on-premises Active Directory and Entra ID. This ensures users and devices are recognized across the hybrid fabric.

* **Entra Connect Sync Status:** ![Entra Connect](entra_connect.png)
* **Hybrid Domain Baseline:** ![AD Setup](Screenshot%202025-12-06%20145951.png)

---

## 2. SCEP & Intune Certificate Infrastructure
Evidence validating the automated certificate enrollment pipeline. This confirms the Intune Certificate Connector is processing requests from the Issuing CA.

* **Intune Certificate Connector Service:** ![Cert Connector Service](Intune%20Certificate%20Connector%20Service.png)
* **Connector Health Verification:** ![Cert Connector Proof](Intune-CertConnector.png)
* **Successful SCEP Enrollment:** ![SCEP Proof](proof_intune_scep.png)
* **Client-Side Certificate Store:** ![SCEP Certs](scep-certs.png)
* **Event Log Audit (Event 4000):** ![SCEP Event Log](event4000-proof-intune-scep.png)

---

## 3. App Proxy & Secure Connector Services
Verification of the outbound-only HTTPS conduit. These logs prove that NDES traffic is securely tunneled without inbound firewall exceptions.

* **App Proxy Connector Status:** ![App Proxy](app%20proxy.png)
* **App Proxy Event Audit (Event 13001):** ![App Proxy Event](event_13001.png)
* **NDES Secure Publishing Proof:** ![App Proxy Proof](proof-app-proxy.png)

---

## 4. Network Management & Edge Baseline
The foundational networking layer, demonstrating the orchestration of the Aruba AOS-CX fabric and initial 802.1X connectivity.

* **Aruba NetEdit Orchestration:** ![NetEdit](netedit.png)
* **AOS-CX 802.1X Authentication (PEAP):** ![CX Proof](cx_proof_sh_port_access_peap.png)
* **Client Connection Baseline:** ![TEAP Windows](teap-windwos.png)

---

## Access Validation-Proof Hub
Validation evidence and configuration exports for the core infrastructure are centralized here to provide a baseline for the Transit and Identity modules.

### Cloud Networking: Evidence & Audit
* **Infrastructure Baseline:** All identity and PKI connectors confirmed active.
* **Service Redundancy:** Validated via hypervisor-level service availability.

---

**Navigation**
[Back to Parent Category](../) | [Back to Main Architecture](../../README.md)