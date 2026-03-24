# Implementation Logic: Core Fabric Components

This directory documents the low-level implementation details of the on-premises and hybrid infrastructure.

---

## 1. Virtualization Foundation
The environment is built on a high-availability Proxmox VE cluster.

* **Compute Resources:** Managed via a central dashboard to ensure high-availability.
![Proxmox VMs](./images/proxmox_vms.png)

## 2. Hybrid Identity and Cloud Integration
Integration with Microsoft Entra ID and Intune is the cornerstone of the Zero Trust model.

* **Identity Synchronization:** Managed via Entra Connect.
![Entra Connect](./images/entra_connect.png)
![Nick Tenant](./images/nick-tenant.png)

* **Enterprise Applications:** Specific app registrations for GlobalProtect and ClearPass.
![Enterprise Apps](./images/enterprise-apps.png)
![Tenant Apps](./images/tenant-apps.png)

## 3. PKI Automation and SCEP Delivery
The certificate lifecycle is automated to support 802.1X and secure management access.

* **Certificate Templates:** Customized for computer and user authentication.
![Certificate Templates](./images/certificate-templates.png)
![CA Template](./images/ca-template.png)

* **NDES and Certificate Connector:**
![Intune Cert Connector](./images/intune-certconnector.png)
![Intune Service](./images/intune-certificate-connector-service.png)

* **Registry Hardening:** MSCEP regedit configurations for SCEP challenges.
![NDES Regedit](./images/ndes_msec_regedit.png)

## 4. Secure Outbound Connectivity (App Proxy)
The Entra Private Network Connector avoids inbound firewall holes.

* **Outbound Tunnel:** Secure path for Intune to communicate with NDES.
![App Proxy](./images/app-proxy.png)
![App Proxy Config](./images/app-proxy-config.png)

## 5. Network Management and Orchestration
* **ArubaOS-CX Switching:** Orchestrated via NetEdit.
![NetEdit](./images/netedit.png)

* **Wireless Fabric:** IAP portal and Aruba Central integration.
![IAP Portal](./images/iap_portal.png)
![Aruba Central AP](./images/screenshot-aruba_cental_ap.png)

---

## General Implementation Evidence
![Palo Service Account](./images/service_account_palo.png)
![Connection Proof](./images/proof_connection.png)
![Subscription Info](./images/sub.png)