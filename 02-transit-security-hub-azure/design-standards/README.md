# Design Standards: Azure Transit Security Hub

This document defines the architectural standards for the Cloud Security Edge. It outlines the requirements for high-availability transit, stateful inspection, and the dual-tunnel hybrid backbone used to support cloud-resident identity services.

---

## 1. Architectural Challenges and Solutions
The transit hub was developed specifically to overcome critical engineering hurdles inherent in hybrid Zero Trust deployments:

| Challenge | Architectural Solution |
| :--- | :--- |
| **Hybrid 802.1X Latency:** Authenticating on-premises users against a Cloud PDP (`CPPM3`) often fails due to timeout or routing gaps. | **Identity-Aware Transit:** The NVA cluster is mandated to prioritise and steer RADIUS/TACACS+ traffic through the high-speed S2S tunnel directly to the Identity segment. |
| **Asymmetric Routing:** Azure's default system routes often bypass NVAs on return paths, breaking stateful inspection. | **Load Balancer Sandwich:** Using an ILB as the Next Hop ensures all return traffic is steered back to the active NVA. |
| **NVA Failover Latency:** Manual UDR updates during an NVA failure cause significant downtime. | **Standard Load Balancer (SLB) HA:** Automated health probes (TCP 22) trigger sub-second failover between NVA instances. |
| **"Short-cut" Traffic:** On-premises traffic often bypasses security by routing directly from the Gateway to Spokes. | **GatewaySubnet UDR:** Forcing a 0.0.0.0/0 route on the GatewaySubnet to the NVA Trust IP eliminates the "short-cut." |
| **Visibility Gaps:** Standard VPN Gateways provide limited Layer 7 visibility into internet-bound traffic. | **Direct S2S Tunnel (Tunnel 1):** Terminating the tunnel directly on the NVA ensures L7 inspection for all outbound flows. |

## 2. Hybrid Identity Mandate (Cloud-Resident PDP)
A core requirement of this project is the centralisation of the Policy Decision Point (PDP) in Azure to support a "Cloud-First" identity strategy.

* **On-Premises Edge Auth:** The local Aruba CX switches and APs must perform 802.1X/TEAP handshakes against the `CPPM3` node located in the Azure Hub.
* **Transit Performance:** The Azure Transit Hub must ensure low-latency pathing for RADIUS packets to prevent EAP timeouts at the access layer.
* **Symmetric Identity Flow:** The Palo Alto NVA cluster must maintain stateful session symmetry for all authentication traffic to ensure User-ID mappings are correctly harvested for security policy enforcement.

## 3. High Availability (HA) Standards
The transit hub must maintain session state and connectivity during an NVA instance failure. 

* **The Load Balancer Sandwich:** To achieve NVA clustering in Azure, a "sandwich" design is required. 
    * **External Standard Load Balancer (ELB):** Interfaces with the internet/untrust zone to manage ingress traffic.
    * **Internal Standard Load Balancer (ILB):** Interfaces with the trust zone to act as the Next Hop for all internal traffic.
* **Health Monitoring:** Health probes must be configured to ensure the load balancers only forward traffic to active, responsive security nodes.

## 4. Hybrid Connectivity: Dual-Tunnel Strategy
To balance management accessibility with high-security inspection, the architecture mandates a dual-tunnel approach between on-premises and Azure.

* **Tunnel 1 (Direct NVA):** Terminated directly on the NVA cluster. This is the primary path for high-security workloads and internet breakout, ensuring Layer 7 inspection at the Azure edge.
* **Tunnel 2 (VPN Gateway):** Terminated on the native Azure VPN Gateway. This provides a resilient management backbone and serves as the BGP peering point for the internal network.

## 5. Traffic Steering and Inspection Mandate
The design enforces a "No Shortcut" policy where 100% of inter-zone and hybrid traffic must be inspected.

* **Forced Tunneling:** All Spoke-to-Spoke and Spoke-to-On-Premises traffic must be steered to the NVA via User-Defined Routes (UDRs) pointing to the ILB VIP.
* **Gateway Ingress:** Traffic arriving from the VPN Gateway must be intercepted at the GatewaySubnet and redirected to the NVA before it reaches its destination.

## 6. Security Policy Standards
* **User-ID:** Security policies must be identity-aware, leveraging the ClearPass/Active Directory integration.
* **Inspection Profiles:** All transit traffic must be subjected to the "Strict" security profile, including Antivirus, Anti-Spyware, and Vulnerability Protection.

---
[Back to Top](#design-standards-azure-transit-security-hub) | [Back to Main Architecture](../../README.md)