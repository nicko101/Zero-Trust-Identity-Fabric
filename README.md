# Zero-Trust Identity Fabric
## Full-Stack Enterprise ZTNA and Hybrid Cloud Transit Hub

![Hero Image](./diagrams/Automating%20Zero%20Trust%20Access%20Blueprint.png)

---

## 1. Executive Summary: The Technical Flow
This architecture demonstrates a production-hardened **Hybrid Zero Trust** environment. It orchestrates a high-fidelity handshake between on-premises infrastructure and **Azure Cloud Services** to enforce identity-based access across a distributed fabric.

### ➔ [View Advanced Engineering Analysis](./docs/engineering-analysis.md)

---

## 2. Key Technical Challenges Solved
* **NVA High Availability & Resilience:** Implementation of a "Load Balancer Sandwich" in Azure (External and Internal Standard Load Balancers) to front the **Palo Alto VM-Series** cluster.
* **Hybrid 802.1X Identity Flow:** Orchestrates the routing of on-premises RADIUS/TEAP traffic through the S2S tunnel to a cloud-resident **ClearPass VM (CPPM3)**.
* **NVA Split-Tunnel Routing:** Management of dual IPsec tunnels (Backbone vs. Security Breakout) using Symmetric PBF.
* **Outbound-Only Cert Retrieval:** Utilises **Entra Private Network Connector (App Proxy)** for internal SCEP/NDES retrieval without inbound firewall holes.

---

## 3. Repository Structure (TOC)
This repository is organised into modular engineering phases:

* **[01-infrastructure-core](./01-infrastructure-core/):** Physical and virtualised baseline. Includes Proxmox hypervisor networking, VyOS edge logic, and on-premises firewalling.
* **[02-transit-security-hub-azure](./02-transit-security-hub-azure/):** The cloud security edge. Contains the Azure Transit VNet, NVA HA Load Balancers, and Gateway UDR steering logic.
* **[03-identity-policy-engine](./03-identity-policy-engine/):** The Zero Trust brain. Documents ClearPass (CPPM) services, TEAP/EAP-TLS authentication methods, and Intune/Entra ID integration.
* **[artifacts](./artifacts/):** Centralised backup of all raw configuration files (Azure ARM JSON, Palo Alto XML, and AOS-CX TXT).
* **[docs](./docs/):** Technical deep dives, engineering analysis, and SIEM/SecOps observability documentation.

---

## 4. Reproducibility & Environment Artifacts
This repository is designed for full environmental audit and reproducibility. All core configuration files are centralised in the [Artifacts Folder](./artifacts/).

* **Azure IaC:** JSON ARM templates for the Transit Hub and NVA HA Cluster.
* **Security Policy:** Palo Alto XML configuration exports.
* **Network State:** Aruba AOS-CX CLI configurations and ClearPass service exports.

---

## 5. Detailed Engineering Deep-Dives
* [Proxmox Networking & VyOS NAT Logic](./docs/tech-notes/proxmox-networking.md)
* [PKI Lifecycle: SCEP, NDES, and Intune](./docs/tech-notes/pki-scep-lifecycle.md)
* [Hybrid Transit: Palo Alto to Azure S2S](./docs/tech-notes/palo-azure-transit.md)
* [Identity Edge: 802.1X & DUR Logic](./docs/tech-notes/8021x-clearpass-cx.md)
* [Palo Alto Security & User-ID Integration](./docs/tech-notes/palo-alto-security.md)
* [Secure Guest WiFi Anatomy](./docs/tech-notes/guest-wifi-anatomy.md)
* [Entra App Proxy for NDES](./docs/tech-notes/entra-app-proxy.md)
* [ArubaOS-CX Switching & Port Access](./docs/tech-notes/aruba-cx-switching.md)
* [ClearPass Advanced Services](./docs/tech-notes/clearpass-advanced-services.md)
* [SIEM & SecOps: Centralised Logging](./docs/tech-notes/secops-siem-observability.md)
* [Offensive Validation & Pentesting](./docs/tech-notes/pentesting-offensive-validation.md)