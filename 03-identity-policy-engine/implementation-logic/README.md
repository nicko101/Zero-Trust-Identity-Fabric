# Implementation Logic: Identity and Policy Orchestration

This directory documents the technical configuration of the Policy Decision Point (PDP). It details the ClearPass service logic, the Intune Graph API integration, and the captive portal orchestration used to enforce Zero Trust access.

---

## 1. ClearPass (CPPM) Service Orchestration
The CPPM cluster handles the RADIUS handshake and evaluates enforcement policies based on user identity and real-time device health.

* **Service Pipeline:** A consolidated view of the active RADIUS and Web-Auth services configured within the Policy Manager.

![CPPM Services](./images/cppm_services_list.png)

* **RADIUS Trust:** The server certificate used to establish secure TEAP (EAP-FAST) and EAP-TLS tunnels with endpoints.

![RADIUS Certificate](./images/cppm_vm_radius_cert.png)

* **Deep Dive:** [ClearPass Advanced Services](../../docs/tech-notes/clearpass-advanced-services.md)

## 2. Microsoft Intune and Cloud Bridge
To achieve posture-based access, the on-premises ClearPass engine is integrated with the Microsoft Graph API.

* **Intune Extension v6.4.1:** The primary API bridge used for real-time compliance lookups during authentication.

![Intune Extension](./images/intune_extension.png)

* **Attribute Mapping:** Specific JSON attributes pulled from Intune to determine the health and authorization level of a device.

![Intune Attributes](./images/Intune-Attributes.png)

* **SCEP Profiles & App Proxy:** The Intune-side configuration for automated certificate delivery via the outbound-only Entra App Proxy tunnel.

![SCEP Profiles](./images/intune-scep-profiles.png)

![App Proxy Config](./images/app-proxy-config.png)

* **Deep Dive:** [Entra App Proxy](../../docs/tech-notes/entra-app-proxy.md) | [PKI SCEP Lifecycle](../../docs/tech-notes/pki-scep-lifecycle.md)

## 3. Guest Access and Captive Portal
The environment provides a secure, isolated guest flow utilizing dynamic VLAN assignment and a web-based captive portal.

* **Portal Configuration:** The ClearPass Guest skin and field logic for visitor registration.

![Guest Portal](./images/cppm_guest_portal_config.png)

* **IAP Integration:** Aruba Instant AP configuration for intercepting and redirecting unauthenticated guest clients to the portal.

![IAP Portal Config](./images/config_IAP_guest_captive_portal.PNG)

---
[Back to Top](#implementation-logic-identity-and-policy-orchestration) | [Back to Main Architecture](../../README.md)