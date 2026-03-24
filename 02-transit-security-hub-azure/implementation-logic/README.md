# Implementation Logic: Transit and Routing Architecture

This section documents the configuration logic used to establish the Hybrid Transit Hub. It demonstrates how the environment bypasses single-tunnel bottlenecks to achieve enterprise-grade load balancing and symmetric split-routing.

---

## 1. The Enterprise Transit Solution
To eliminate the scaling limitations of terminating IPsec directly on an NVA, the cryptography is offloaded to the Azure VPN Gateway, and inner-IP traffic is load-balanced to the Palo Alto firewalls.

* **On-Premises Tunnels:**
![On-Premises Tunnels](./images/onprem_tunnells.png)
* **NVA Azure Tunnels:**
![Azure Tunnels](./images/nva-tunnels.png)
* **Internal Load Balancer (ILB):**
![Azure ILB](./images/az_ilb_vip.png)

## 2. Split-Routing and The Gateway Subnet Hack
This architecture uses User-Defined Routes (UDRs) and the "Longest Prefix Match" rule to force-tunnel ingress traffic from the VPN Gateway through the NVA cluster.

* **Gateway Routing Table (UDR):**
![VPN Gateway RT](./images/rt-vpngw.png)
* **Spoke Routing Table (UDR):**
![Spoke RT](./images/rt-spoke-ilb.png)
* **Route Table Logic (BGP):**
![Route Table Spokes](./images/az-route-table-spokes.png)
![Route Table VPNGW](./images/az-route-table-vpngw.png)

## 3. Azure VNet Peering Fabric
Specific peering relationships allow the Hub and Spokes to communicate strictly through the NVA inspection point.

* **Gateway to NVA Peering:** ![Gateway to NVA](./images/azure_peering_vpng-nva.png)
* **NVA to Spoke Peering:** ![NVA to Spoke](./images/azure_peering_nva-spoke.png)
* **Peering to Spoke Evidence:** ![Peering Spoke](./images/peering-tospoke.png)

## 4. Symmetric Return via Policy-Based Forwarding (PBF)
PBF ensures traffic returns symmetrically, preventing the stateful firewalls from dropping out-of-state packets.

* **Trust VR:** ![Trust VR](./images/nva_vr_trust.png)
* **Untrust VR:** ![Untrust VR](./images/nva-vr-untrust.png)
* **PBF Rulebase:** ![PBF Rules](./images/nva_pbf.png)

## 5. Security Services and Translation
* **BGP Adjacency:**
![BGP Status](./images/bgp_establishd.png)
* **NAT Rulebase:**
![NAT Rules](./images/nva-NAT.png)
* **External Dynamic Lists (EDL):**
![Palo EDL](./images/palo_EDL_out.png)
* **Load Balancer Persistence:**
![Session Persistence](./images/il_session_persisstence_none_traffic_lb.png)

---

## Navigation
[Back to Main Architecture](../../README.md)