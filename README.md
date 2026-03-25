# Zero-Trust Identity Fabric
## Full-Stack Enterprise ZTNA and Hybrid Cloud Transit Hub

![Hero Image](./diagrams/Automating%20Zero%20Trust%20Access%20Blueprint.png)

---

## 1. Executive Summary: The Technical Flow
This architecture demonstrates a production-hardened Hybrid Zero Trust environment. It orchestrates a high-fidelity handshake between on-premises infrastructure and Azure Cloud Services to enforce identity-based access across a distributed fabric.

[View Advanced Engineering Analysis](./docs/engineering-analysis.md)

---

## 2. Breaking the Perimeter Paradox
This architecture was designed to solve the inherent security and routing limitations of legacy enterprise networks. The comparison below outlines the core engineering shifts implemented in this fabric.

![Legacy vs Zero Trust](./diagrams/legacy_vs_ZT.png)

---

## 3. Key Technical Challenges Solved
* NVA High Availability and Active/Active Resilience: Implemented an Azure Load Balancer Sandwich (External and Internal Standard Load Balancers) to front the Palo Alto VM-Series. Crucially, this design bypasses the standard Active/Passive VPN limitation, allowing both NVAs to actively load-balance Spoke-to-On-Premises traffic simultaneously without relying on health-probe failover hacks.
* NVA Split-Tunnel and Symmetric Routing: Solved the inherent asymmetric routing challenges of dual IPsec tunnels terminating on an NVA cluster. Engineered Symmetric Policy-Based Forwarding (PBF) and split-tunnel logic to ensure return VPN traffic is pinned to the correct stateful NVA, keeping both firewalls active and optimized.
* Hybrid 802.1X Identity Flow: Orchestrates the routing of on-premises RADIUS/TEAP traffic through the S2S tunnel to a cloud-resident ClearPass VM (CPPM3).
* Outbound-Only Cert Retrieval: Utilizes Entra Private Network Connector (App Proxy) for internal SCEP/NDES retrieval, eliminating the need for vulnerable inbound firewall policies.

---

## 4. Repository Structure (TOC)
This repository is organised into modular engineering phases:

* [01-infrastructure-core](./01-infrastructure-core/): Physical and virtualised baseline. Includes Proxmox hypervisor networking, VyOS edge logic, and on-premises firewalling.
* [02-transit-security-hub-azure](./02-transit-security-hub-azure/): The cloud security edge. Contains the Azure Transit VNet, NVA HA Load Balancers, and Gateway UDR steering logic.
* [03-identity-policy-engine](./03-identity-policy-engine/): The Zero Trust brain. Documents ClearPass (CPPM) services, TEAP/EAP-TLS authentication methods, and Intune/Entra ID integration.
* [artifacts](./artifacts/): Centralised backup of all raw configuration files (Azure ARM JSON, Palo Alto XML, and AOS-CX TXT).
* [docs](./docs/): Technical deep dives, engineering analysis, and SIEM/SecOps observability documentation.

---

## 5. Reproducibility and Environment Artifacts
This repository is designed for full environmental audit and reproducibility. All core configuration files are centralised in the [Artifacts Folder](./artifacts/).

* Azure IaC: JSON ARM templates for the Transit Hub and NVA HA Cluster.
* Security Policy: Palo Alto XML configuration exports.
* Network State: Aruba AOS-CX CLI configurations and ClearPass service exports.

---

## 6. Detailed Engineering Deep-Dives
* [Proxmox Networking and VyOS NAT Logic](./docs/tech-notes/proxmox-networking.md)
* [PKI Lifecycle: SCEP, NDES, and Intune](./docs/tech-notes/pki-scep-lifecycle.md)
* [Hybrid Transit: Palo Alto to Azure S2S](./docs/tech-notes/palo-azure-transit.md)
* [Identity Edge: 802.1X and DUR Logic](./docs/tech-notes/8021x-clearpass-cx.md)
* [Palo Alto Security and User-ID Integration](./docs/tech-notes/palo-alto-security.md)
* [Secure Guest WiFi Anatomy](./docs/tech-notes/guest-wifi-anatomy.md)
* [Entra App Proxy for NDES](./docs/tech-notes/entra-app-proxy.md)
* [ArubaOS-CX Switching and Port Access](./docs/tech-notes/aruba-cx-switching.md)
* [ClearPass Advanced Services](./docs/tech-notes/clearpass-advanced-services.md)
* [SIEM and SecOps: Centralised Logging](./docs/tech-notes/secops-siem-observability.md)
* [Offensive Validation and Pentesting](./docs/tech-notes/pentesting-offensive-validation.md)

---
[back to top](#zero-trust-identity-fabric)