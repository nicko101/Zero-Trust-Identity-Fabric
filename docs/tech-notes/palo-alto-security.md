# Palo Alto NVA Routing and Traffic Engineering (Azure Transit)

This document details the routing architecture, traffic engineering, and inspection logic implemented on the Palo Alto VM-Series NVAs within the Azure Transit Security Hub.

---

## Overview

The NVAs operate as **stateless firewalls (no session synchronisation)** and act as the central inspection and routing layer for all hybrid traffic.

To support this model, the design enforces:

- Deterministic traffic paths
- Symmetric routing per session
- Full Layer 7 inspection
- No routing shortcuts

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
  Trust VR to Untrust VR

This enforces strict separation between internal and external traffic flows.

---

## Route Table Separation

Each VR maintains independent routing logic.

### Trust VR

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

![NVA Tunnels](https://raw.githubusercontent.com/nicko101/Zero-Trust-Identity-Fabric/main/02-transit-security-hub-azure/implementation-logic/images/nva-tunnels.png)

![On-Prem Tunnels](https://raw.githubusercontent.com/nicko101/Zero-Trust-Identity-Fabric/main/02-transit-security-hub-azure/implementation-logic/images/onprem_tunnells.png)

---

## Internal Source NAT for Symmetric Return

To support the stateless load-balanced design, Source NAT is applied to internal traffic flows.

- Traffic is translated to the NVA Trust interface IP
- Return traffic is forced back to the same NVA instance

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