# Deep-Dive: Hybrid Transit & Symmetric Routing Logic

## 1. Dual-Path Architecture: Logic & Path Isolation
To optimize security inspection and prevent appliance pinning, the fabric utilizes two distinct IPsec paths between the On-Premises SDDC and Azure. This separation ensures that "Dirty" internet traffic and "Clean" internal transit are handled by different architectural logic.

| Tunnel ID | Termination Point | Primary Role |
| :--- | :--- | :--- |
| **Tunnel 200** | **Azure VPN Gateway** | **Internal Data Plane:** On-premises to Spoke transit (via ILB VIP). |
| **Tunnel 300** | **Palo Alto NVA** | **Internet Egress:** Direct path for on-premises internet breakout/inspection. |

## 2. The Symmetry & NVA Scaling Challenge
In standard NVA-only tunnel deployments, encrypted traffic is "pinned" to a single firewall instance. This creates a bottleneck and prevents horizontal scaling across a cluster.

### The Solution: Gateway Decoupling
By routing the **Internal Data Plane via Tunnel 200**, we decouple the decryption from the inspection:
1. **Decryption:** Handled by the native Azure VPN Gateway.
2. **Symmetry:** Traffic is handed off to the **Internal Load Balancer (ILB)**.
3. **Inspection:** The ILB distributes "clear" flows symmetrically across the **Active/Active Palo Alto NVAs**, maintaining session state even as the cluster scales.

## 3. Policy-Based Forwarding (PBF)
On the **On-Premises Palo Alto**, we implemented PBF rules to ensure traffic enters the correct tunnel based on the intended destination, bypassing the standard RIB (Routing Information Base) to maintain path isolation:

* **Internal Match:** Ingress from `Trust` zone destined for Azure Spokes (`10.10.0.0/16`).
* **Action:** Forward to `tunnel.200` (VPN Gateway Path).
* **Internet Match:** Ingress from `Trust` zone destined for `any` (Non-RFC1918).
* **Action:** Forward to `tunnel.300` (NVA Direct Path).

## 4. Azure User-Defined Routes (UDR)
To close the loop, Spoke subnets in Azure utilize UDRs to force the return path. By setting the **Azure NVA Trust IP** as the **Next Hop** for all traffic destined for on-premises, we ensure the Palo Alto cluster remains "in-path" for the entire session lifecycle, preventing asymmetric drops.

---

**Navigation**

---

[Back to Engineering Analysis](../engineering-analysis.md) | [Back to Main Architecture](../../README.md)