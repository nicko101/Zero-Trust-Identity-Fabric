![Unified Fabric Flow](./diagrams/5_overview.png)

*Figure: End-to-end Zero Trust architecture integrating Aruba edge, ClearPass identity, Azure transit hub, and inspected resource access.*

Note: For simplicity, the diagram abstracts the on-premises Palo Alto firewall and dual IPsec tunnel design. In the full implementation, traffic traverses an on-prem NGFW and is split across two tunnels: a direct NVA tunnel for outbound inspection and a VPN Gateway path for load-balanced internal traffic.

---

## Overview

This project demonstrates a production-style **Zero Trust architecture spanning on-premises infrastructure and Microsoft Azure**, built to enforce identity-driven access across a hybrid environment.

It replaces traditional perimeter-based security with a model where:

- Identity is the control plane  
- Device compliance determines access  
- Network segmentation is dynamically enforced  

---

## Architecture Summary

The fabric integrates:

- **On-Premises Infrastructure (Proxmox SDDC)**  
  Palo Alto NGFW, Aruba AOS-CX, ClearPass, AD DS, PKI (NDES)

- **Cloud Platform (Azure)**  
  Hub-and-spoke transit architecture with load-balanced NVAs and VPN Gateway  

- **Identity & Compliance (Entra ID + Intune)**  
  Cloud-based device compliance and certificate lifecycle management  

Together, these components form a **fully integrated Zero Trust access model**.

---

## Key Design Outcomes

| Capability | Implementation |
| :--- | :--- |
| Identity-Driven Access | ClearPass + Intune (Graph API) |
| Dynamic Segmentation | Aruba DUR (Downloadable User Roles) |
| Hybrid Transit Control | Dual-path routing (NVA + VPN Gateway) |
| Symmetric Inspection | ILB + stateless NVA design |
| Secure Certificate Delivery | SCEP via Entra App Proxy (outbound-only) |

---

## Core Challenges Solved

### Cloud-Native Device Authorization ([ClearPass Intune Extension](./03-identity-policy-engine/))  
Traditional AD cannot validate cloud-only devices.

→ ClearPass integrates with Intune via the Intune Extension to validate identity and compliance in real time.

---

### Dynamic Micro-Segmentation ([Aruba DUR](./03-identity-policy-engine/))  
Static VLANs and ACLs do not scale and break Zero Trust principles.

→ Aruba switches dynamically enforce access using Downloadable User Roles (DUR).

---

### Hybrid Transit & IPSec Pinning ([Selective Dual-Path Routing](./02-transit-security-hub-azure/))  
Standard NVA designs suffer from asymmetric routing and tunnel pinning.

→ Dual-path routing:
- Internet traffic → pinned to a primary NVA  
- Internal traffic → load-balanced via VPN Gateway + ILB  

---

### Inspected Hybrid Identity Flows ([RADIUS over VPN](./02-transit-security-hub-azure/))  
Extending authentication into Azure introduces visibility gaps and bypass risks.

→ RADIUS/PEAP traffic is routed via VPN Gateway and steered through Palo Alto NVAs, ensuring all identity flows are inspected before reaching the cloud-resident ClearPass Policy Manager.

---

### Secure Certificate Lifecycle ([Entra App Proxy + Intune SCEP](./03-identity-policy-engine/))  
Traditional SCEP/NDES requires inbound exposure.

→ Entra App Proxy enables outbound-only certificate delivery with Intune-managed SCEP profiles.
---

## Repository Structure

This repository is organized into modular architecture domains:

- **[01 Infrastructure Core](./01-infrastructure-core/)**  
  Physical and virtual infrastructure baseline  

- **[02 Transit Security Hub (Azure)](./02-transit-security-hub-azure/)**  
  Hybrid routing, NVA inspection, and traffic engineering  

- **[03 Identity Policy Engine](./03-identity-policy-engine/)**  
  ClearPass, Intune integration, and Zero Trust enforcement  

- **[Artifacts](./artifacts/)**  
  Raw configuration files (ARM, XML, CLI)  

- **[Engineering Docs](./docs/)**  
  Deep dives and technical analysis  

---

## Engineering Deep Dives

Detailed breakdowns of key components and design decisions:

- [802.1X ClearPass CX](./docs/tech-notes/8021x-clearpass-cx.md)  
- [ClearPass Advanced Services](./docs/tech-notes/clearpass-advanced-services.md)  
- [Palo Alto Azure Transit](./docs/tech-notes/palo-azure-transit.md)  
- [PKI SCEP Lifecycle](./docs/tech-notes/pki-scep-lifecycle.md)  
- [Entra App Proxy](./docs/tech-notes/entra-app-proxy.md)  
- [Guest WiFi Anatomy](./docs/tech-notes/guest-wifi-anatomy.md)  
- [SIEM & Observability](./docs/tech-notes/secops-siem-observability.md)  

---

## Validation & Proof

Each module includes full validation evidence, including:

- Routing validation (BGP, traceroute, flow logs)  
- Identity validation (ClearPass Access Tracker)  
- Compliance validation (Intune integration)  
- End-to-end Zero Trust flow verification  

- [Infrastructure Core Validation](./01-infrastructure-core/validation-proof/README.md)  
- [Transit Hub Validation](./02-transit-security-hub-azure/validation-proof/README.md)  
- [Identity Engine Validation](./03-identity-policy-engine/validation-proof/README.md)  

---

## Environment Baseline

- **Cloud:** Microsoft Azure  
- **Identity:** Entra ID + Intune  
- **On-Prem:** Proxmox SDDC (Palo Alto, ClearPass, AD DS, PKI)  
- **Network:** Aruba AOS-CX switching + Aruba IAP  

---

## Project Context

This is a **100% greenfield deployment**, built end-to-end as a solo project.

Scope includes:

- Full infrastructure build (compute, networking, security)  
- Hybrid identity integration (Entra ID, Intune, PKI)  
- Network access control (ClearPass, 802.1X, DUR)  
- Azure transit architecture and traffic engineering  

---

[View Full Engineering Analysis](./docs/engineering-analysis.md)