# Implementation Logic: Infrastructure Core

This directory documents the as-built configuration and technical logic for the foundation of the Zero Trust Identity Fabric. It details how the virtualization, identity, and switching layers are integrated into a functional security ecosystem.

---

This module represents the **foundational layer of the fabric**, providing the compute, identity, and network control systems that all higher-layer services depend on.

It integrates directly with:

- The Azure Transit Security Hub for hybrid connectivity and traffic inspection  
- The Identity Policy Engine for authentication, authorization, and compliance enforcement  

Together, these layers form the complete Zero Trust architecture.

---

## 1. Hypervisor Deployment (Proxmox VE)
The infrastructure is anchored on a **Proxmox VE** hypervisor to provide a hardware-agnostic, snapshot-capable environment for security testing.

* **Logic:** Proxmox hosts the core Domain Controller, the ClearPass Policy Decision Point, and the Palo Alto NVA. This allows for rapid rollbacks during iterative policy testing and ensures consistent resource allocation across the fabric.
* **Inventory Validation:** The following VM list confirms the deployment of the Policy Decision Point (ClearPass), Security Edge (Palo Alto HA), and Identity services (DC, NDES, Entra Connect).

![Proxmox VM Inventory](../validation-proof/images/proxmox_vms.png)

* **Deep Dive:** [Proxmox Networking Logic](../../docs/tech-notes/proxmox-networking.md)

## 2. Wired Fabric Orchestration (Aruba CX and NetEdit)
The wired edge is comprised of **ArubaOS-CX** switches, managed through a central orchestration engine.

* **Logic:** **NetEdit** is utilized as the primary automation engine. Beyond monitoring, it is used to execute **Python-based scripts** that push standardized AAA/RADIUS and VLAN configurations.
* **Deep Dive:** [Aruba CX Switching and Orchestration](../../docs/tech-notes/aruba-cx-switching.md)

![NetEdit Scripting](../validation-proof/images/netedit.png)

## 3. Policy Decision Point (ClearPass)
**Aruba ClearPass (CPPM)** serves as the Policy Decision Point (PDP) for the entire fabric.

* **Logic:** ClearPass is deployed as a virtual appliance with isolated management and data planes. Its implementation centers on the **Intune Extension** and **Graph API** integration.
* **Deep Dive:** [ClearPass Advanced Services](../../docs/tech-notes/clearpass-advanced-services.md) | [802.1X Integration](../../docs/tech-notes/8021x-clearpass-cx.md)

## 4. Secure Tunnel and Trust Lifecycle (App Proxy and SCEP)
This module automates the delivery of identity certificates without requiring inbound firewall rules.

* **Logic:** The **Microsoft Entra Private Network Connector (App Proxy)** establishes an outbound-only HTTPS tunnel.
* **Deep Dive:** [Entra App Proxy](../../docs/tech-notes/entra-app-proxy.md) | [PKI SCEP Lifecycle](../../docs/tech-notes/pki-scep-lifecycle.md)

![App Proxy Status](../validation-proof/images/app-proxy.png)

## 5. Security Enforcement Edge (Palo Alto NVA)
The **Palo Alto VM-Series** provides Layer 7 inspection and serves as the transit security hub.

* **Logic:** The NVA is implemented as the primary gateway for all inter-zone and hybrid-cloud traffic using **User-ID** integration.
* **Deep Dive:** [Palo Alto Security Logic](../../docs/tech-notes/palo-alto-security.md)

## 6. Observability and Validation
Continuous monitoring and security validation are integrated into the core fabric.

* **Logic:** The environment is instrumented with centralized logging for real-time observability and subjected to **offensive security simulations**.
* **Deep Dive:** [SecOps and Observability](../../docs/tech-notes/secops-siem-observability.md) | [Offensive Validation](../../docs/tech-notes/pentesting-offensive-validation.md)

[Back to Top](#implementation-logic-infrastructure-core) | [Back to Main Architecture](../../README.md)