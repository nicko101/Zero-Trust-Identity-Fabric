# Implementation Logic: Selective Dual-Path Routing

### Cloud Networking
This document outlines the technical configuration and routing logic required to execute the dual-path split-tunnel architecture. 

### Evidence & Audit
Validation evidence and configuration exports for this service are centralized in the module-level hub. The blueprint images are in the infra proof folder.

### UDR Configuration and Flow Execution
To achieve selective Active/Active load balancing without state synchronization, the routing tables are split by destination intent:

1. Internet Egress Route (NVA1 Pinned)
A User Defined Route is applied to the on-premises edge directing all 0.0.0.0/0 traffic across the primary IPsec tunnel. This terminates directly on NVA1, ensuring consistent stateful inspection for all outbound web traffic.

2. Hybrid Internal Route (Load Balanced)
A separate User Defined Route directs all 10.0.0.0/8 (Azure Spoke) traffic across the secondary IPsec tunnel terminating on the Azure VPN Gateway. 
The VPN Gateway strips the IPsec encapsulation and hands the clear-text traffic to the Azure Internal Load Balancer.
The ILB uses a 5-tuple hash to distribute the internal traffic symmetrically across both NVA1 and NVA2.

### Access Validation-Proof Hub
To review the routing table exports and traffic logs confirming this symmetry:
* [Access Validation-Proof Hub](../validation-proof/README.md)
* [Back to Parent Category](../)
* [Back to Main Lab Architecture](../../)