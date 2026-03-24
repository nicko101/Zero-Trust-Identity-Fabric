# Deep-Dive: ArubaOS-CX Switching & Hybrid PKI Strategy

This document details the switching fabric configuration and the specific cryptographic trust model used to enable zero-touch **Downloadable User Roles (DUR)**.

## 1. The Hybrid PKI Strategy
The lab utilizes a two-tier certificate strategy to balance granular internal control with seamless automated orchestration.

* **Client/User Identity (Internal PKI):** 802.1X certificates for workstations are issued by the **Internal Microsoft CA**.
    * **Benefit:** Absolute control over the certificate lifecycle, non-exportable private keys (TPM-backed), and no public certificate costs for internal endpoints.
* **Server/DUR Identity (Public CA):** The ClearPass server uses a publicly signed **Let’s Encrypt** certificate for `cppm.duckdns.org`.
    * **Benefit:** Because the AOS-CX switches already contain built-in public Root CAs (like **ISRG Root X1**), they automatically trust the ClearPass HTTPS server. This eliminates the need to manually export/import an internal CA root onto every switch in the fabric, simplifying the orchestration of DUR.

## 2. DUR HTTPS Flow & Certificate Validation
For the switch to download a role, it must act as an HTTPS client and verify the identity of the ClearPass portal.

* **DNS Prerequisite:** The switch uses `ip dns server-address 10.0.5.250` to resolve `cppm.duckdns.org`.
* **Trust Profiles:**
    ```text
    crypto pki ta-profile LE-TA
        ta-certificate (ISRG Root X1 / Let's Encrypt R12)
    ```
* **Authentication:** The switch uses the `cx-dur` service account to securely authenticate to the ClearPass API and pull the JSON role definition (VLANs, ACLs, and QoS).

## 3. Port-Access Enforcement (802.1X/MAC-Auth)
Every access port is a "Colorless Port," reacting to the identity provided by the Policy Decision Point (ClearPass).

* **802.1X (EAP-TLS):** Prioritized for domain-managed devices.
* **MAC-Auth:** Fallback for guests and IoT devices.
* **Dynamic Authorization:** `radius dyn-authorization enable` is active, allowing ClearPass to send CoA (Change of Authorization) requests to the switch to instantly update a device's role if its compliance status changes in Intune.

## 4. Stability and Loop Prevention
* **Loop Protection:** `loop-protect` is active on all access-facing interfaces to prevent broadcast storms within the Proxmox virtual bridges.
* **Interim Accounting:** `aaa accounting port-access start-stop interim 2` provides the SIEM and ClearPass with real-time session visibility.

---

[Back to Engineering Analysis](../engineering-analysis.md)