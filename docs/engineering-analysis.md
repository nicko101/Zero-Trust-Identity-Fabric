# Zero Trust Identity Fabric: Engineering Analysis

## Executive Summary

This document provides a technical analysis of a full-stack Zero Trust architecture spanning on-premises infrastructure and Microsoft Azure.

The solution is built around three tightly integrated control planes:

- Identity (ClearPass + Intune) → [Identity Policy Engine](../../03-identity-policy-engine/)  
- Network Enforcement (Aruba + DUR) → [Identity Policy Engine](../../03-identity-policy-engine/)  
- Hybrid Transit (Palo Alto + Azure) → [Transit Security Hub](../../02-transit-security-hub-azure/)  

The architecture replaces traditional perimeter-based models with a system where identity, device compliance, and traffic inspection are enforced consistently across all access paths.

It addresses key enterprise challenges including:

- Cloud-native device authentication without domain dependency  
- Secure certificate lifecycle without inbound exposure  
- Hybrid routing symmetry and IPSec tunnel pinning  
- Inspection of both application and identity traffic flows  

The result is a cohesive Zero Trust fabric where policy enforcement is applied uniformly across on-premises and cloud environments.

---

## Architectural Framework

The architecture is built on the principle that identity—not network location—is the primary security boundary.

It is composed of two integrated environments:

### 1. On-Premises Infrastructure (Proxmox SDDC)

The on-premises environment provides the physical and virtual foundation of the architecture.

Core components include:

- Networking: Aruba AOS-CX switching and Aruba Instant Access Points (IAP)  
- Security: Palo Alto Next-Generation Firewall (NGFW)  
- Identity and Policy: ClearPass Policy Manager, Active Directory Domain Services (AD DS), and PKI (NDES)  

---

### 2. Cloud Platform (Microsoft Azure)

Azure operates as the transit and identity extension layer, hosting critical control plane components.

Core components include:

- Transit Architecture: Hub-and-spoke design with load-balanced NVAs and VPN Gateway  
- Identity and Compliance: Entra ID and Microsoft Intune  
- Cloud Identity Node: Dedicated ClearPass Publisher deployed in an Azure spoke  

---

## Core Architectural Capabilities

The architecture enforces Zero Trust through the following capabilities:

| Capability | Implementation |
| :--- | :--- |
| Identity-Driven Access | ClearPass integration with Intune via Microsoft Graph API |
| Dynamic Segmentation | Aruba Downloadable User Roles (DUR) |
| Hybrid Transit Control | Dual-path routing (NVA + VPN Gateway) |
| Symmetric Inspection | Internal Load Balancer with stateless NVA design |
| Secure Certificate Lifecycle | SCEP via Entra App Proxy (outbound-only) |

---

## Problem-Solution Analysis

### 1. Cloud-Native Device Authorization

Challenge:  
Traditional Active Directory cannot validate cloud-only (Entra ID joined) devices.

Solution:  
ClearPass integrates with the Intune Extension to query Microsoft Graph API, validating both identity and real-time compliance before granting access.

---

### 2. Dynamic Micro-Segmentation

Challenge:  
Static VLANs and ACLs are operationally complex and incompatible with Zero Trust principles.

Solution:  
Aruba switches dynamically enforce access using Downloadable User Roles (DUR), enabling per-session segmentation without manual configuration.

---

### 3. Hybrid Transit and IPSec Pinning

Challenge:  
Traditional NVA-based IPsec termination leads to asymmetric routing and tunnel pinning, limiting scalability and causing session drops.

Solution: Dual-Path Routing

Traffic is separated based on intent:

- Internet-bound traffic:  
  Routed via a dedicated tunnel terminating on a primary NVA for deterministic, stateful inspection  

- Internal hybrid traffic:  
  Routed via Azure VPN Gateway and distributed across NVAs using an Internal Load Balancer  

This ensures:

- Symmetric routing  
- Horizontal scalability  
- Full inspection across all traffic paths  

---

### 4. Inspected Hybrid Identity Flows (RADIUS over VPN)

Challenge:  
In many hybrid designs, authentication traffic bypasses inspection, creating visibility and control gaps.

Solution:  

- RADIUS/PEAP authentication traffic originates from on-premises network devices  
- Traffic traverses the hybrid connection via VPN Gateway  
- All authentication flows are steered through Palo Alto NVAs before reaching ClearPass in Azure  

This ensures that identity traffic is subject to the same inspection and Zero Trust enforcement as application traffic.

---

### 5. Secure Certificate Lifecycle

Challenge:  
Traditional SCEP/NDES implementations require inbound exposure, increasing attack surface.

Solution:  
Entra App Proxy enables outbound-only certificate retrieval, allowing Intune-managed devices to securely obtain certificates without exposing internal PKI infrastructure.

---

## Hybrid Transit Architecture

### The Core Challenge

Standard hybrid deployments suffer from:

- Asymmetric routing  
- Limited scalability  
- Loss of session state across NVAs  

---

### The Design: Selective Dual-Path Routing

The architecture introduces a traffic-aware routing model:

- Tunnel 1 (Direct NVA):  
  Handles internet-bound traffic requiring strict inspection  

- Tunnel 2 (VPN Gateway):  
  Handles internal hybrid traffic and BGP route exchange  

Traffic is distributed via an Internal Load Balancer to ensure:

- Per-session symmetry  
- Load-balanced NVA utilisation  
- Consistent inspection enforcement  

---

### Identity Traffic Integration

Unlike conventional architectures:

- Identity traffic is not treated as a special case  
- RADIUS flows follow the same inspected path as application traffic  
- ClearPass in Azure acts as a cloud-resident Policy Decision Point (PDP)  

This ensures end-to-end consistency in policy enforcement across all traffic types.

---

## Validation and Operational Proof

Validation artefacts are available within each module:

- [Transit Validation](../../02-transit-security-hub-azure/validation-proof/)  
- [Identity Validation](../../03-identity-policy-engine/validation-proof/)  

The architecture is validated through multiple layers:

- Network Validation:  
  BGP adjacency, traceroute, and flow logs confirm correct routing behaviour  

- Identity Validation:  
  ClearPass Access Tracker verifies successful authentication and policy enforcement  

- Compliance Validation:  
  Intune confirms device compliance prior to access  

- Traffic Inspection:  
  Palo Alto logs confirm that all hybrid and identity traffic is inspected  

---

## Conclusion

This architecture demonstrates a production-grade implementation of Zero Trust principles across a hybrid environment.

By tightly integrating identity, network enforcement, and transit controls, the design ensures that:

- All access decisions are identity-driven  
- All traffic paths are inspected and controlled  
- Hybrid connectivity does not introduce security gaps  

The result is a fully integrated Zero Trust fabric, where identity and network controls operate as a single, cohesive system.

---

## Navigation

[Back to Main Architecture](../../README.md)  
[Identity Policy Engine](../../03-identity-policy-engine/)  
[Transit Security Hub](../../02-transit-security-hub-azure/)  
