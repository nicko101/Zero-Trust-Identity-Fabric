# Implementation Logic: Policy Engine & Cloud Integration

This directory documents the technical configuration of the Policy Decision Point (PDP). It details the ClearPass service logic, the Intune Graph API integration, and the captive portal orchestration for guest access.

---

## 1. ClearPass (CPPM) Service Orchestration
The CPPM cluster handles the RADIUS handshake and evaluates enforcement policies based on user identity and device health.

* **Service Pipeline:** A consolidated view of the RADIUS and Web-Auth services.
![CPPM Services](./images/cppm_services_list.png)
* **RADIUS Trust:** The server certificate used for secure EAP-TLS/PEAP handshakes.
![RADIUS Certificate](./images/cppm_vm_radius_cert.png)
* **Resource Deployment:** JSON export of the CPPM resource group logic.
`rg_cppm.json`

## 2. Microsoft Intune & Cloud Bridge
To achieve posture-based access, ClearPass is integrated with the Microsoft Graph API via the Intune Extension.

* **Intune Extension v6.4.1:** The primary bridge for compliance lookups.
![Intune Extension](./images/intune_extension.png)
* **Attribute Mapping:** Specific JSON attributes pulled from Intune to determine device health.
![Intune Attributes](./images/Intune-Attributes.png)
* **SCEP Profiles:** Intune-side configuration for automated certificate delivery.
![SCEP Profiles](./images/intune-scep-profiles.png)
* **App Proxy Logic:** Outbound-only connectivity for the NDES/CPPM cloud handshake.
![App Proxy Config](./images/app-proxy-config.png)

## 3. Guest Access & Captive Portal
The environment provides a secure, isolated guest flow using a web-based captive portal.

* **Portal Configuration:** The ClearPass Guest skin and field logic.
![Guest Portal](./images/cppm_guest_portal_config.png)
* **IAP Integration:** Aruba Instant AP configuration for redirecting guest clients.
![IAP Portal Config](./images/config_IAP_guest_captive_portal.PNG)

## 4. Authentication Proof
* **Windows PEAP Verification:** Verification of a successful PEAP-MSCHAPv2 handshake from a Windows 10/11 client.
![PEAP Proof](./images/proof_win_client_peap.png)

---

## 5. Supplementary Implementation Evidence
These snapshots document the iterative configuration of NDES certificates and general service status:

* **NDES/MSCEP Certificates:** ![NDES Certs](./images/ndes_server_mscep_certs.png)
* **VM Run-State Log:** `sh_run_cppm_vm_w.txt`
* **Historical Logic Snapshots:** (See the `images/` folder for timestamped captures of the AD DS sync and service-level regedit tweaks.)