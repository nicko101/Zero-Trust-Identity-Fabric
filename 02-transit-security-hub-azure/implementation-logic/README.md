# Implementation Logic: Azure Transit Security Hub

This section documents how traffic is routed, inspected, and distributed across the Azure Transit Security Hub using a dual-path architecture.

---

## 🔹 Dual-Path Routing Model (Core Design)

The architecture separates traffic flows based on destination to overcome IPSec tunnel pinning and enable scalable inspection.

### Path 1: Internet-Bound Traffic (Direct NVA Tunnel)

- On-premises internet traffic is routed via a dedicated IPSec tunnel terminating directly on the NVA  
- Ensures deterministic egress and consistent stateful inspection  

---

### Path 2: Internal Hybrid Traffic (VPN Gateway Path)

- Internal traffic (Spoke ↔ On-Prem) is routed via the Azure VPN Gateway  
- Traffic is redirected using UDRs to the Internal Load Balancer (ILB)  
- ILB distributes traffic across NVAs for inspection  

This enables:

- Load balancing across NVAs  
- Symmetric routing per session  
- Full inspection of hybrid traffic  

---

### Why This Design Exists

A single tunnel design causes:

- IPSec tunnel pinning  
- Asymmetric routing  
- Session drops in stateless NVA designs  

The dual-path model resolves this by separating:

- Internet flows → pinned to one NVA  
- Internal flows → load-balanced across NVAs  

---

## 🔹 High Availability & Load Distribution (ILB)

The Internal Load Balancer provides a consistent next hop for all internal traffic.

Configured with **Session Persistence = None (5-tuple hash)**:

- Per-session load balancing  
- Flow consistency (same NVA per session)  
- No mid-session movement  

This behaviour is critical for stateless firewall operation.

For configuration details and validation:

[Azure Load Balancer Deep Dive](../../docs/tech-notes/azure-routing-and-udr.md)

---

## 🔹 Traffic Steering (UDRs)

User-Defined Routes (UDRs) enforce inspection by redirecting all hybrid traffic to the NVA layer.

- GatewaySubnet UDR intercepts VPN Gateway traffic  
- Spoke subnets route `0.0.0.0/0 → ILB`  

Due to Azure limitations, a standard default route (`0.0.0.0/0`) cannot be applied to the GatewaySubnet.  
Instead, split default routes are used:

- `0.0.0.0/1`  
- `128.0.0.0/1`  

These combined routes ensure full traffic coverage and enforce inspection.

For full routing logic and validation:

[Azure Routing and UDR Deep Dive](../../docs/tech-notes/azure-routing-and-udr.md)

---

## 🔹 NVA Routing & Inspection

Palo Alto NVAs perform Layer 7 inspection and act as the central enforcement point.

The NVA layer implements:

- Virtual Router separation (Trust / Untrust)  
- Inter-VR routing for traffic domain control  
- Policy-Based Forwarding (PBF) for deterministic path selection  
- Internal Source NAT (SNAT) for session symmetry  

All hybrid and inter-spoke traffic is forced through this layer.

For detailed routing logic and traffic engineering:

[NVA Routing and PBF Deep Dive](../../docs/tech-notes/palo-alto-security.md)

---

## 🔹 Hybrid Connectivity (IPSec Tunnels & Static Routing)

The hybrid backbone is built using two IPSec tunnels with explicit routing control.

- **Direct NVA Tunnel**  
  Used for internet-bound traffic  

- **VPN Gateway Tunnel**  
  Used for internal hybrid traffic (Spoke ↔ On-Prem)  

Traffic steering is controlled using:

- Static routes  
- Policy-Based Forwarding (PBF)  

This ensures:

- Deterministic traffic paths  
- No dependency on dynamic routing protocols  
- Full inspection and path control  

---

## 🔹 Identity Traffic (Context Only)

Authentication traffic (e.g. RADIUS) follows the internal hybrid path:

VPN Gateway → ILB → NVA → ClearPass

This ensures:

- Identity traffic is inspected  
- Symmetric routing is preserved  
- No authentication flows bypass security controls  

For full authentication flow and packet-level analysis:

[ClearPass RADIUS Flow Deep Dive](../../docs/tech-notes/clearpass-radius-flow.md)

---

[Back to Top](#implementation-logic-azure-transit-security-hub)  
[Back to Main Architecture](../../README.md)