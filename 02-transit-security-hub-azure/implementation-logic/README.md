# Implementation Logic: Azure Transit Security Hub

This section documents how the Azure Transit Security Hub is implemented, including load balancing, routing logic, and hybrid identity traffic flows across the environment.

---

## 🔹 End-to-End Packet Flow (Hybrid 802.1X Authentication)

This section describes the real traffic path taken during an on-premises 802.1X authentication request to the cloud-resident ClearPass node.

1. **Endpoint Authentication Initiation**  
   A client device connects to an Aruba CX switch and initiates 802.1X (TEAP).

2. **RADIUS Request (On-Prem → Azure)**  
   The switch forwards RADIUS traffic across the IPSec tunnel towards Azure.

3. **Gateway Ingress (VPN Gateway)**  
   Traffic arrives at the Azure VPN Gateway and is intercepted by a UDR applied to the GatewaySubnet.

4. **Traffic Steering to NVA**  
   The UDR redirects traffic to the Internal Load Balancer VIP, ensuring inspection by the NVA cluster.

5. **Load Balancer Distribution**  
   The ILB forwards the flow to one of the Palo Alto NVAs based on hash-based persistence.

6. **Stateful Inspection & Policy Enforcement**  
   The NVA processes the RADIUS request, applies security policy, and forwards it towards the Identity segment.

7. **ClearPass Processing (CPPM3)**  
   The cloud-hosted ClearPass node validates device identity and posture via Intune/Graph API.

8. **Response Path (Symmetric Return)**  
   The response is routed back through the same NVA instance via the ILB, preserving session state.

9. **Authentication Success**  
   The Aruba switch receives the Access-Accept and applies the appropriate role (DUR).

This flow ensures:
- Symmetric routing across NVAs  
- Full Layer 7 inspection  
- Cloud-based identity enforcement without inbound exposure  

---

## 1. High Availability & Traffic Distribution (Azure Load Balancer)

The NVA cluster is fronted by an Internal Load Balancer (ILB) to provide a consistent next hop for all internal traffic flows.

This ensures symmetric routing and prevents session drops in a stateless NVA design.

* **ILB VIP Configuration:**  
  The ILB provides a private front-end IP that acts as the gateway for the hub-and-spoke architecture.  
![Azure ILB VIP](./images/az_il_vip.png)

### Load Balancer Session Behaviour

The Azure Internal Load Balancer is configured with **Session Persistence = None**, which uses a 5-tuple hash (source/destination IP, ports, and protocol) to distribute traffic.

This results in:

- **Per-session load balancing:** Each new flow may be directed to a different NVA  
- **Flow consistency:** All packets within a session are processed by the same NVA  
- **No mid-session rebalancing:** Active sessions are not moved between NVAs  

This behaviour is critical in a stateless NVA design, as it ensures session integrity while still distributing load across independent NVA instances.
![LB Session Persistence](./images/il_session_persisstence_none_traffic_lb.png)

---

## 2. Hybrid Identity Solution: Cloud-Resident CPPM3

A core feature of this implementation is the relocation of the Policy Decision Point (PDP) into Azure.

This allows identity decisions to be enforced centrally using cloud-native signals.

* **CPPM3 Azure VM Deployment:**  
  ClearPass is deployed as a dedicated VM in the Azure Identity segment.  
![CPPM Azure VM](./images/cppm+az_vm.png)

* **RADIUS Trust:**  
  A server certificate is installed on `CPPM3` to establish secure TEAP and EAP-TLS tunnels with on-premises endpoints.  
![RADIUS Certificate](./images/cppm_vm_radius_cert.png)

* **Deep Dive:**  
  [ClearPass Advanced Services](../../docs/tech-notes/clearpass-advanced-services.md)

---

## 3. Traffic Steering & Forced Tunnelling (Azure Route Tables)

User-Defined Routes (UDRs) are used to override Azure system routing and enforce inspection through the NVA layer.

This prevents any traffic from bypassing security controls.

* **Gateway Ingress Steering:**  
  A UDR applied to the **GatewaySubnet** intercepts traffic arriving from the VPN Gateway and redirects it to the NVA Trust interface.  
![Azure RT Gateway](./images/az-route-table-vpngw.png)

* **Spoke & Identity Steering:**  
  All subnets use a default route (`0.0.0.0/0`) pointing to the ILB VIP, ensuring all traffic is inspected.  
![Azure RT Spokes](./images/az-route-table-spokes.png)

---

## 4. NVA Routing & Security Policy

The Palo Alto NVAs enforce routing and Layer 7 inspection for all hybrid traffic flows.

This ensures identity traffic is both visible and controlled.

* **Trust VR Logic:**  
  Manages internal routing and ensures traffic destined for the `CPPM3` subnet is correctly forwarded.  
![NVA VR Trust](./images/nva_vr_trust.png)

* **Hybrid Authentication Flow Proof:**  
  Security logs confirm that RADIUS traffic from on-premises switches reaches the Azure-hosted ClearPass node via the NVA.  
![On-Prem to Cloud CPPM Flow](./images/logs_onprem_pa-cppmvm1812.png)

* **Deep Dive:**  
  [Palo Alto Security Logic](../../docs/tech-notes/palo-alto-security.md)

---

## 5. Hybrid Connectivity: S2S Tunnels & BGP

The hybrid backbone is built using a dual-tunnel strategy with dynamic route exchange via BGP.

This enables both controlled traffic steering and resilient connectivity.

* **Tunnel Interface Status:**  
  Shows active IPSec Phase 1 and Phase 2 security associations on the NVA.  
![NVA Tunnels](./images/nva-tunnels.png)

* **BGP Adjacency:**  
  Established between the NVA cluster and Azure VPN Gateway to automate route propagation.  
![BGP Established](./images/bgp_establishd.png)

---

## 6. VNet Peering & Transit Orchestration

Hub-and-spoke connectivity is implemented using VNet peering with gateway transit enabled.

This allows spokes to leverage centralized security and routing.

* **Transit Configuration:**  
  Confirms "Use Remote Gateways" and "Allow Gateway Transit" across VNets.  
![VNet Peering Transit](./images/azure_peering_nva-vpngw.png)

![Spoke Peering](./images/peering-tospoke.png)

---

[Back to Top](#implementation-logic-azure-transit-security-hub) | [Back to Main Architecture](../../README.md)