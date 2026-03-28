# Palo Alto NVA Routing and Traffic Engineering (Azure Transit)

This document details the routing architecture, traffic engineering, and inspection logic implemented on the Palo Alto VM-Series NVAs within the Azure Transit Security Hub.

---

## Overview

The NVAs operate as stateful firewalls without session synchronisation and act as the central inspection and routing layer for all hybrid traffic.

To support this model, the design enforces:

- Deterministic traffic paths
- Symmetric routing per session
- Full Layer 7 inspection
- No routing shortcuts

---

## Hybrid Transit and Dual-Path Routing Logic

To enable scalable inspection and eliminate IPSec tunnel pinning, the architecture implements a dual-path hybrid routing model between On-Premises and Azure.

### Tunnel Design

| Tunnel ID | Hosted On | Terminates At | Role |
| :--- | :--- | :--- | :--- |
| **Tunnel 200** | On-Prem Palo Alto | Azure VPN Gateway | Internal hybrid traffic (Spoke ↔ On-Prem via ILB) |
| **Tunnel 300** | On-Prem Palo Alto | Azure NVA | Internet-bound traffic (direct inspection path) |
| **Tunnel 100** | Azure NVA | On-Prem Palo Alto | Return path for hybrid and inspected traffic |

![NVA Tunnels](https://raw.githubusercontent.com/nicko101/Zero-Trust-Identity-Fabric/main/02-transit-security-hub-azure/implementation-logic/images/nva-tunnels.png)

![On-Prem Tunnels](https://raw.githubusercontent.com/nicko101/Zero-Trust-Identity-Fabric/main/02-transit-security-hub-azure/implementation-logic/images/onprem_tunnells.png)
These screenshots show the tunnel termination points across the on-premises Palo Alto and Azure NVA, illustrating the dual-path architecture and return path via tunnel.100.

---

## The Symmetry and Scaling Challenge

In a traditional NVA-only design:

- Encrypted traffic becomes pinned to a single firewall instance
- Load balancing across NVAs is not possible
- Asymmetric routing causes session drops

---

## The Solution: Gateway Decoupling

The architecture separates **decryption from inspection**:

1. **Decryption**  
   Handled by Azure VPN Gateway (Tunnel 200)

2. **Traffic Distribution**  
   Traffic is forwarded to the Internal Load Balancer (ILB)

3. **Inspection**  
   The ILB distributes flows across NVAs using hash-based load balancing

This enables:

- Horizontal scaling across NVAs
- Symmetric routing per session
- Stable session handling

---

## Azure Return Path Enforcement

To maintain symmetric routing, Azure enforces return paths using User-Defined Routes (UDRs):

- Spoke subnets route on-prem traffic via the Internal Load Balancer (ILB)
- GatewaySubnet routes redirect traffic to the ILB for inspection

This guarantees:

- No asymmetric routing
- NVAs remain in-path for the full session lifecycle
- Consistent inspection across all flows

---

## Virtual Router Design

The NVA is segmented into multiple Virtual Routers (VRs) to separate traffic domains:

- **Trust VR**  
  Handles internal and hybrid traffic (Spoke VNets, On-Prem, Identity services)

- **Untrust VR**  
  Handles internet-bound traffic and NAT egress

This separation ensures deterministic routing and prevents unintended traffic leakage.

![Trust VR Routing](https://raw.githubusercontent.com/nicko101/Zero-Trust-Identity-Fabric/main/02-transit-security-hub-azure/implementation-logic/images/nva_vr_trust.png)

![Untrust VR Routing](https://raw.githubusercontent.com/nicko101/Zero-Trust-Identity-Fabric/main/02-transit-security-hub-azure/implementation-logic/images/nva-vr-untrust.png)

---

## Inter-VR Routing

Traffic is explicitly passed between VRs based on destination:

- Internal traffic enters via the Trust interface
- Internet-bound traffic is forwarded:
  Trust VR → Untrust VR

This enforces strict separation between internal and external traffic flows.

---

## Route Table Separation

Each VR maintains independent routing logic.

### Trust VR

![Trust VR Route Table](https://raw.githubusercontent.com/nicko101/Zero-Trust-Identity-Fabric/main/02-transit-security-hub-azure/implementation-logic/images/nva_vr_trust.png)

Routes to:

- Azure Spokes (via Internal Load Balancer VIP)
- On-premises networks (via tunnel interfaces and VPN Gateway path)
- Azure Health Probe (`168.63.129.16/32`)

Default route:

- `0.0.0.0/0` to `next-vr untrust-vr`

---

### Untrust VR

- Default route to Internet via external interface
- Handles NAT and return traffic
![Trust VR Routing](https://raw.githubusercontent.com/nicko101/Zero-Trust-Identity-Fabric/main/02-transit-security-hub-azure/implementation-logic/images/nva-vr-untrust.png)

---

## Policy-Based Forwarding (PBF)

PBF is used to explicitly control traffic paths and override standard routing behaviour.

PBF rules:

- Match hybrid traffic (Spoke to On-Prem)
- Forward traffic to the correct next-hop (VPN Gateway or direct NVA tunnel)
- Use **Enforce Symmetric Return** to maintain session consistency

This ensures:

- No asymmetric routing
- Stable session handling
- Deterministic traffic flows

![PBF Rules](https://raw.githubusercontent.com/nicko101/Zero-Trust-Identity-Fabric/main/02-transit-security-hub-azure/implementation-logic/images/nva_pbf.png)

---

## Hybrid Traffic Flow Mapping

The NVA enforces the dual-path routing model:

### Internet Traffic
- Routed via direct IPSec tunnel
- Processed in Untrust VR

### Internal Hybrid Traffic
- Routed via VPN Gateway
- Redirected to ILB
- Inspected in Trust VR

This ensures full inspection and symmetric routing across all flows.

---

## Internal Source NAT for Symmetric Return

To support the stateless load-balanced design, Source NAT is applied to internal traffic flows.

- Traffic is translated to the NVA Trust interface IP
- Return traffic is directed back to the same NVA instance

Without this:

- Return traffic may hit a different NVA
- Sessions will fail due to lack of state

This guarantees:

- Session stickiness
- Symmetric routing
- Reliable inspection

![NVA NAT Rules](https://raw.githubusercontent.com/nicko101/Zero-Trust-Identity-Fabric/main/02-transit-security-hub-azure/implementation-logic/images/nva-nat.png)

---

## Related Modules

- [Transit Security Hub](../../02-transit-security-hub-azure/)
- [Implementation Logic](../../02-transit-security-hub-azure/implementation-logic/README.md)

---

**Navigation**

[Back to Tech Notes](./README.md) | [Back to Main Architecture](../../README.md)