# Implementation Logic: Azure Transit Security Hub

This directory documents the implementation of the Azure-based security fabric. It details the routing logic, NVA configuration, and hybrid transit paths required to maintain stateful inspection between the on-premises DC and the Azure cloud spokes.

---

## 1. Azure VNet Routing and Transit Logic
The Hub and Spoke architecture utilizes User-Defined Routes (UDR) to force-tunnel traffic through the Palo Alto NVAs for inspection.

* **Spoke Route Tables:** Directs all inter-spoke and egress traffic to the NVA Internal Load Balancer (ILB).
![Spoke Route Table](./images/az-route-table-spokes.png)
* **VPN Gateway Routing:** Ensures return traffic from the S2S tunnel is sent back through the NVA cluster.
![VPN Gateway Route Table](./images/az-route-table-vpngw.png)
* **Spoke-to-ILB Integration:**
![RT Spoke ILB](./images/rt-spoke-ilb.png)

## 2. VNet Peering and Fabric Connectivity
Full-mesh peering between the Transit Hub and Spoke VNets, configured with "Use Remote Gateways" and "Allow Forwarded Traffic."

* **NVA to Spoke Peering:**
![NVA Spoke Peering](./images/azure_peering_nva-spoke.png)
* **VPNGW to NVA Transit:**
![VPNGW NVA Peering](./images/azure_peering_vpng-nva.png)

## 3. Hybrid Connectivity and BGP Handshake
The connection between the on-premises Palo Alto and Azure VPN Gateway is managed via dynamic BGP routing over IPsec.

* **BGP Adjacency:** Confirms the route exchange between the DC and Azure.
![BGP Established](./images/bgp_establishd.png)
* **IPsec Tunnel Status:**
![On-Prem Tunnels](./images/onprem_tunnells.png)
![NVA Tunnels](./images/nva-tunnels.png)

## 4. NVA Security Logic (Palo Alto)
Stateful Layer 7 inspection and Symmetric Policy Based Forwarding (PBF) ensure traffic is inspected without causing asymmetric routing issues.

* **Virtual Routers (Trust/Untrust):**
![VR Trust](./images/nva_vr_trust.png)
![VR Untrust](./images/nva-vr-untrust.png)
* **Symmetric PBF:** Critical for maintaining session state across the NVA cluster.
![NVA PBF](./images/nva_pbf.png)
* **NAT Policies:**
![NVA NAT](./images/nva-NAT.png)
* **External Dynamic Lists (EDL):**
![Palo EDL](./images/palo_EDL_out.png)

## 5. High Availability and Load Balancing
The Azure Internal Load Balancer (ILB) acts as the Next Hop for all UDRs, distributing traffic across the Active/Active NVA pair.

* **ILB VIP Configuration:**
![Azure ILB VIP](./images/az_ilb_vip.png)
* **Session Persistence:** Configured to ensure traffic for the same flow returns to the same NVA instance.
![Session Persistence](./images/il_session_persisstence_none_traffic_lb.png)