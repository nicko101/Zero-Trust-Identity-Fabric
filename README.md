# Zero-Trust Identity Fabric
## Full-Stack Enterprise ZTNA and Hybrid Cloud Transit Hub

![Blueprint](./diagrams/Automating%20Zero%20Trust%20Access%20Blueprint.png)

---

## 1. Executive Summary: The Technical Flow
This architecture demonstrates a production-hardened Hybrid Zero Trust environment. It orchestrates a high-fidelity handshake between on-premises infrastructure and Azure Cloud Services to enforce identity-based access across a distributed fabric.

[View Advanced Engineering Analysis](./docs/engineering-analysis.md)

---

## 2. Repository Structure
This repository is organized into modular engineering phases:

* **[01-infrastructure-core](./01-infrastructure-core/):** Physical and virtualized baseline. Includes Proxmox hypervisor networking, VyOS edge logic, and on-premises firewalling.
* **[02-transit-security-hub-azure](./02-transit-security-hub-azure/):** The cloud security edge. Contains the Azure Transit VNet, NVA HA Load Balancers, and Gateway UDR steering logic.
* **[03-identity-policy-engine](./03-identity-policy-engine/):** The Zero Trust brain. Documents ClearPass (CPPM) services, TEAP/EAP-TLS authentication methods, and Intune/Entra ID integration.
* **[artifacts](./artifacts/):** Centralized backup of all raw configuration files (Azure ARM JSON, Palo Alto XML, and AOS-CX TXT).
* **[docs](./docs/):** Technical deep dives, engineering analysis, and SIEM/SecOps observability documentation.

---

## 3. Breaking the Perimeter Paradox
This architecture was designed to solve the inherent security and routing limitations of legacy enterprise networks. The comparison below outlines the core engineering shifts implemented in this fabric.

| Architectural Domain | Legacy Enterprise Network | Zero-Trust Identity Fabric |
| :--- | :--- | :--- |
| **Identity Context** | Static IP Address and VLAN | User, Device Health, and Dynamic Context |
| **Trust Boundary** | Static Network Perimeter | Dynamic, Micro-Segmented Enclaves |
| **Access Enforcement** | Static Allow/Deny ACLs | Dynamic User Roles (DUR) and TEAP Chaining |
| **Routing and Traffic** | Asymmetric, Hub-and-Spoke | Symmetric Policy-Based Forwarding (PBF) |
| **Hybrid Scaling** | IPsec on Firewall (Active/Passive bottleneck) | IPsec on Azure Gateway (Active/Active ILB) |

> Visual Reference:
> ![Legacy vs Zero Trust](./diagrams/legacy_vs_ZT.png)

---

## 4. Key Technical Challenges Solved
* **Active/Active Hybrid Transit Architecture**
  * **The Challenge:** Terminating hybrid IPsec tunnels directly on a standalone pair of NVAs behind a load balancer forces an Active/Passive state. To maintain tunnel stability, one NVA must be restricted and removed from the load balancing pool via health probe manipulation, leaving expensive firewall compute idle.
  * **The Solution:** Decoupled the decryption layer by terminating the tunnel on the native Azure VPN Gateway. The gateway routes the decrypted traffic directly to an Internal Load Balancer (ILB) VIP. This allows the load balancer to function normally, distributing hybrid traffic across both Palo Alto NVAs for true Active/Active inspection.

* **Symmetric Routing and Split-Tunnels**
  Solved asymmetric routing challenges for dual IPsec tunnels. Engineered Policy-Based Forwarding (PBF) to ensure return traffic is pinned to the correct stateful firewall.

* **Hybrid 802.1X Identity Flow**
  Orchestrated the secure routing of on-premises RADIUS/TEAP traffic to a cloud-resident ClearPass policy engine.

* **Outbound-Only Certificate Retrieval**
  Utilized Entra App Proxy for internal SCEP/NDES retrieval, entirely eliminating the need to open vulnerable inbound firewall ports.

---

## 5. Prerequisites and Environment Baseline
To fully replicate this environment using the provided infrastructure-as-code and configuration artifacts, the following baseline is required:

* **Cloud Infrastructure:** A Microsoft Azure Subscription with permissions to provision Virtual Network Gateways, Standard Load Balancers, and compute resources.
* **Identity and Access:** A Microsoft Entra ID tenant (P1/P2 recommended for Entra Private Network Connector support) and an active Intune MDM environment.
* **On-Premises Hypervisor:** A bare-metal host running Proxmox VE to simulate the on-premises datacenter, host the VyOS edge router, and manage local switching.
* **Appliance Licensing:** * Palo Alto VM-Series (PAN-OS) evaluation licenses (for both Azure Transit NVAs and the on-premises edge).
  * Aruba ClearPass Policy Manager (CPPM) VM images and evaluation licenses.

---

## 6. Reproducibility and Environment Artifacts
This repository is designed for full environmental audit and reproducibility. All core configuration files are centralized in the **[Artifacts Folder](./artifacts/)**.

* **Azure IaC:** JSON ARM templates for the Transit Hub and NVA HA Cluster.
* **Security Policy:** Palo Alto XML configuration exports.
* **Network State:** Aruba AOS-CX CLI configurations and ClearPass service exports.

---

## 7. Detailed Engineering Deep-Dives
* [Proxmox Networking and VyOS NAT Logic](./docs/tech-notes/proxmox-networking.md)
* [PKI Lifecycle: SCEP, NDES, and Intune](./docs/tech-notes/pki-scep-lifecycle.md)
* [Hybrid Transit: Palo Alto to Azure S2S](./docs/tech-notes/palo-azure-transit.md)
* [Identity Edge: 802.1X and DUR Logic](./docs/tech-notes/8021x-clearpass-cx.md)
* [Palo Alto Security and User-ID Integration](./docs/tech-notes/palo-alto-security.md)
* [Secure Guest WiFi Anatomy](./docs/tech-notes/guest-wifi-anatomy.md)
* [Entra App Proxy for NDES](./docs/tech-notes/entra-app-proxy.md)
* [ArubaOS-CX Switching and Port Access](./docs/tech-notes/aruba-cx-switching.md)
* [ClearPass Advanced Services](./docs/tech-notes/clearpass-advanced-services.md)
* [SIEM and SecOps: Centralized Logging](./docs/tech-notes/secops-siem-observability.md)
* [Offensive Validation and Pentesting](./docs/tech-notes/pentesting-offensive-validation.md)