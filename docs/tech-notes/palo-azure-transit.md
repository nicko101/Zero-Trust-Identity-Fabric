# Deep-Dive: Hybrid Transit & Symmetric Routing Logic

## 1. Dual-Tunnel Architecture
To separate management traffic (AD/DNS) from production traffic (Spokes), we established two parallel S2S IPsec tunnels:

| Tunnel ID | Type | Role |
| :--- | :--- | :--- |
| **Tunnel 200** | VNET Gateway | BGP-driven path for management and control plane. |
| **Tunnel 300** | NVA (Palo Alto) | High-performance path for inspected data plane traffic. |

## 2. The Symmetry Challenge (PBF)
Standard destination-based routing in Azure is insufficient for this setup. If traffic enters the lab via Tunnel 300 but tries to return via Tunnel 200, the session is dropped as "Asymmetric."

### The Solution: Policy-Based Forwarding (PBF)
On the **On-Premises Palo Alto**, we implemented a PBF rule to override the routing table:
* **Match:** Ingress from `Trust Zone` destined for `10.10.0.0/16` (Azure).
* **Action:** Forward to `tunnel.300`.
* **Result:** Traffic is pinned to the security tunnel, maintaining stateful session consistency.

## 3. Azure User-Defined Routes (UDR)
We use a UDR table on all Spoke subnets to force traffic to the Azure NVA Trust IP as the **Next Hop**, ensuring 100% of spoke traffic is inspected by the Palo Alto before leaving the cloud.

---
**Navigation**
[Back to Engineering Analysis](../engineering-analysis.md) | [Back to Main Architecture](../../README.md)