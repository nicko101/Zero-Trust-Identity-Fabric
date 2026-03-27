# ClearPass RADIUS Flow: Hybrid Authentication Deep Dive

This document provides a detailed breakdown of the end-to-end RADIUS authentication flow in a hybrid Zero Trust environment.

It explains how on-premises endpoints authenticate against a cloud-resident ClearPass Policy Decision Point (PDP), while maintaining full inspection and routing symmetry across the Azure Transit Hub.

---

## Overview

The authentication flow spans multiple control planes:

- On-premises access layer (Aruba CX switches / APs)  
- Hybrid transport (IPSec tunnels + Azure Transit Hub)  
- Cloud-resident identity services (ClearPass in Azure)  
- External identity and compliance sources (Intune / Microsoft Graph)  

This enables a **cloud-first identity model** while preserving network-level enforcement.

---

## Authentication Flow (Step-by-Step)

### 1. Endpoint Initiation

- A device connects to an Aruba CX switch or wireless AP  
- 802.1X authentication begins using TEAP (EAP-TLS + MSCHAPv2)  

---

### 2. RADIUS Request (Access Layer)

- The switch acts as the RADIUS client  
- A RADIUS request is generated and forwarded towards ClearPass  

---

### 3. Hybrid Transport (On-Prem → Azure)

- Traffic traverses the IPSec tunnel  
- Enters Azure via the VPN Gateway  

---

### 4. Transit Hub Inspection

- GatewaySubnet UDR redirects traffic to the Internal Load Balancer (ILB)  
- ILB distributes traffic across Palo Alto NVAs  
- NVA performs:
  - Layer 7 inspection  
  - Security policy enforcement  

---

### 5. Policy Decision Point (ClearPass in Azure)

- RADIUS request reaches ClearPass (CPPM3) in Azure  
- ClearPass performs:
  - Certificate validation (EAP-TLS)  
  - User authentication (MSCHAPv2 / AD)  
  - Device compliance check via Intune Extension (Graph API)  

---

### 6. Authorization Decision

- Access-Accept → Role (DUR) assigned  
- Access-Reject → Deny or quarantine  

---

### 7. Response Path (Symmetric Return)

- Response flows back via:
  - NVA → ILB → VPN Gateway → On-prem  

- Same NVA instance is maintained (session symmetry)

---

### 8. Network Enforcement

- Aruba switch applies:
  - VLAN assignment  
  - Downloadable User Role (DUR)  
  - ACL enforcement  

---

## Key Design Principles

### Identity as the Control Plane

Access decisions are based on:

- Device identity (certificate)  
- User identity  
- Compliance state (Intune)  

---

### Full Traffic Inspection

- All RADIUS traffic is inspected by NVAs  
- No authentication traffic bypasses security controls  

---

### Symmetric Routing Requirement

Required due to stateless NVA design, enforced via:

- Internal Load Balancer  
- UDR-based traffic steering  

---

### Cloud-Resident Policy Engine

- ClearPass is deployed in Azure  
- Enables centralized identity and compliance enforcement  

---

## Why This Architecture Matters

Traditional NAC designs:

- Depend on on-prem AD  
- Lack visibility into cloud-managed devices  
- Often bypass inspection  

This design:

- Extends NAC into the cloud  
- Enforces Zero Trust principles  
- Maintains full visibility and control  

---

## Related Modules

- [Transit Security Hub](../../02-transit-security-hub-azure/)  
- [Identity Policy Engine](../../03-identity-policy-engine/)  

---

## Key Takeaway

Authentication is a distributed, inspected, and identity-driven process spanning on-premises and cloud environments.

---

[Back to Engineering Docs](../README.md)  
[Back to Main Architecture](../../README.md)