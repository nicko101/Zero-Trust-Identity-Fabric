# Transit Security Hub (Azure)

### Cloud Networking

This module defines the Azure Transit Security Hub, which acts as the central routing and security control plane for hybrid connectivity.

The design focuses on a custom dual-path routing strategy to address IPSec tunnel pinning and asymmetric routing issues, while maintaining full traffic inspection and high availability.

---

## 🔹 Identity Traffic Flow (RADIUS over VPN)

Unlike traditional designs where authentication traffic bypasses inspection:

- RADIUS traffic is routed via VPN Gateway  
- Traffic is steered through Palo Alto NVAs  
- ClearPass (in Azure) receives only inspected authentication flows  

This ensures identity traffic is subject to the same Zero Trust controls as application traffic.

## 🔹 Architecture: Dual-Path Split-Tunnel Logic

In standard deployments, routing all traffic through a single IPSec tunnel to a load-balanced Network Virtual Appliance (NVA) layer results in tunnel pinning. This causes asymmetric routing when return traffic is processed by a different NVA, leading to stateful firewall drops.

To address this, the architecture separates traffic flows based on intent:

### **Path 1: Internet-Bound Traffic (Pinned to NVA1)**  
Internet-bound traffic from on-premises is routed through a dedicated IPSec tunnel terminating directly on NVA1.  
This ensures consistent stateful inspection and avoids asymmetric return paths from external destinations.

### **Path 2: Internal Hybrid Traffic (Load-Balanced via VPN Gateway)**  
Spoke-to-on-premises traffic is routed through a secondary IPSec tunnel terminating on the Azure VPN Gateway.  

The VPN Gateway forwards decrypted traffic to the Azure Internal Load Balancer (ILB), which distributes flows across both NVAs.

This ensures:
- Symmetric routing through the same NVA per session  
- Load distribution across both instances  
- Full inspection of internal hybrid traffic  

---

## 🔹 Design Outcome

This approach enables:

- **Selective Active/Active Behaviour:** Internal traffic is distributed across both NVAs  
- **Deterministic Internet Egress:** Internet traffic remains pinned for consistent inspection  
- **Symmetric Flow Enforcement:** Required for stateless firewall operation  
- **Full Traffic Inspection:** No hybrid traffic bypasses the NVA layer  

This design maximises NVA utilisation while maintaining session integrity in a stateless architecture.

---

## 🔹 Module Structure

- **[Design Standards](./design-standards/)**  
  Defines architectural requirements, routing policies, and high-availability principles  

- **[Implementation Logic](./implementation-logic/)**  
  Details load balancer configuration, route tables, and hybrid identity traffic flow  

- **[Validation Proof](./validation-proof/)**  
  Provides logs and evidence validating routing behaviour, inspection, and identity flows  

---

## 🔹 Access Validation Hub

To review validation evidence for split-routing, NVA inspection, and hybrid identity flows:

- [Validation Proof](./validation-proof/README.md)  
- [Back to Parent Category](../../)  
- [Back to Main Lab Architecture](../../../)