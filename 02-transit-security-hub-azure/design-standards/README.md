# Design Standards: Azure Transit Security Hub

This section defines the architectural principles behind the Azure Transit Hub, which acts as the **central security and routing plane** for hybrid identity-driven access.

The design prioritises:
- Stateful inspection of all hybrid traffic  
- Symmetric routing across NVAs  
- High availability without session loss  
- Support for cloud-resident identity services (ClearPass in Azure)  

---

## 1. Architectural Challenges and Solutions

| Challenge | Architectural Solution |
| :--- | :--- |
| **Hybrid 802.1X Latency** | **Identity-Aware Transit:** RADIUS/TACACS+ traffic is prioritised and steered via the high-speed S2S tunnel directly to the Azure-hosted identity services. |
| **Asymmetric Routing** | **Load Balancer Sandwich:** An Internal Load Balancer ensures return traffic is consistently routed back through the same NVA, maintaining session state. |
| **NVA Failover Latency** | **Automated Health Probes:** Load Balancer probes enable sub-second failover between NVAs without manual intervention. |
| **"Short-cut" Traffic** | **GatewaySubnet UDR Enforcement:** A 0.0.0.0/0 route ensures all traffic is forced through the NVA, eliminating inspection bypass. |
| **Visibility Gaps** | **Direct NVA Tunnel:** A dedicated IPSec tunnel terminates on the NVA to guarantee Layer 7 inspection for outbound and inter-site flows. |

---

## 2. Hybrid Identity Mandate (Cloud-Resident PDP)

A core design principle is the centralisation of the **Policy Decision Point (PDP)** in Azure to support a cloud-first identity model.

- On-premises Aruba CX switches and APs perform 802.1X/TEAP authentication directly against **ClearPass (CPPM3)** in the Azure Hub  
- The Palo Alto NVA cluster enforces **symmetric, stateful inspection** to preserve User-ID mappings for downstream security policy enforcement  

This ensures identity remains the primary control plane, not network location.

---

## 3. High Availability Model (Stateless Active/Active NVAs)

The NVA layer operates as **load-balanced, standalone firewalls** (no session synchronisation).

- **Load Balancer Sandwich:** External + Internal Load Balancers distribute traffic across NVAs  
- **Health Probes:** Ensure traffic is only forwarded to responsive instances  
- **Symmetry Requirement:** Traffic must remain symmetric to avoid stateful session drops  

While commonly referred to as active/active, this is a **stateless active/active design** enforced through traffic engineering.

---

## 4. Connectivity Model (High-Level)

The Azure Transit Hub is built on a hub-and-spoke architecture, integrating on-premises infrastructure with cloud-resident security and identity services.

Key connections include:

- **On-Premises ↔ Azure NVA (Direct IPSec Tunnel):**  
  Used for internet-bound and high-security traffic requiring deterministic inspection  

- **On-Premises ↔ Azure VPN Gateway:**  
  Used for internal hybrid connectivity (Spoke ↔ On-Prem traffic and identity flows)  

- **VPN Gateway ↔ Internal Load Balancer (ILB):**  
  Traffic is redirected via UDRs to ensure inspection by the NVA layer  

- **NVA ↔ Spoke VNets:**  
  All application and identity traffic is routed through NVAs before reaching workloads  

- **NVA ↔ Identity Segment (ClearPass):**  
  Ensures authentication traffic is inspected before reaching the Policy Decision Point  

This connectivity model enforces a centralised inspection point while maintaining controlled and deterministic traffic paths.

---

## 5. Hybrid Connectivity: Dual-Tunnel Strategy

The design enforces a strict **“no shortcut” policy**:

- **Forced Tunnelling:**  
  All Spoke-to-Spoke and Spoke-to-On-Prem traffic is routed via NVAs using UDRs  

- **Gateway Ingress Control:**  
  Traffic entering via VPN Gateway is intercepted and redirected to NVAs before reaching workloads  

This guarantees **100% inspection coverage across all hybrid and intra-cloud flows**.

---

## 6. Identity Traffic Inspection Requirement

Authentication traffic is treated as a first-class data flow within the transit architecture and is subject to the same inspection and routing controls as application traffic.

- RADIUS/PEAP traffic is routed via the VPN Gateway (internal path)  
- Traffic is steered through the NVA cluster using GatewaySubnet UDRs  
- ClearPass (CPPM3) is only reachable via inspected paths  

This ensures:

- Identity traffic does not bypass security controls  
- Full Layer 7 visibility is maintained for authentication flows  
- Symmetric routing is preserved across NVAs  

This design extends Zero Trust principles to the identity plane, not just application traffic.

For detailed packet-level analysis and authentication flow: [ClearPass RADIUS Flow Deep Dive](../../docs/tech-notes/clearpass-radius-flow.md)

---

* [Back to Transit Security Hub](../README.md)  
* [Back to Main Lab Architecture](../../README.md)