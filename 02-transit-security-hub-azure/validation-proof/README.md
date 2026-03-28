# Validation Proof: Transit Security Hub (Azure)

This folder contains the technical evidence and engineering blueprints validating the Multi-Tunnel Transit Hub and the split-routing architectural solution between On-Premises and Azure.

---

## 🔹 Validation Objectives

This section validates that the Azure Transit Security Hub meets the core architectural guarantees defined in the design:

- **Symmetric Routing:** All flows return via the same NVA instance to preserve state  
- **Full Traffic Inspection:** No hybrid or inter-zone traffic bypasses the NVA layer  
- **Hybrid Identity Reachability:** On-premises authentication traffic successfully reaches the cloud-resident ClearPass node  
- **Split-Path Routing Behaviour:** Internet and internal traffic follow distinct, controlled paths  
- **High Availability:** Traffic continues to flow during NVA or path changes  

Each validation artefact below maps directly to one or more of these guarantees.

---

## 1. Hybrid Connectivity & Macro Architecture

Confirms that the hybrid control plane (IPSec + BGP) is stable and capable of supporting identity and application traffic flows between on-premises and Azure.

* **S2S Tunnel Status:**  
![Connection Proof](./images/proof_connection.png)

* **BGP Adjacency (VPNGW):**  
![VPNGW BGP](./images/palo-azure_vpngw_bgp.png)

---

## 2. Split-Routing Validation (Selective Dual-Path Behaviour)

Validates that traffic is correctly separated between the NVA-inspected path and the VPN Gateway path, solving IPSec tunnel pinning while maintaining symmetric routing.

* **Split-Routing Logic Analysis:**  
![Split Routing Analysis](./images/proof_split_routing.png)

* **NVA Pathing Verification:**  
![NVA vs Gateway Pathing](./images/proof_split_routing_nva_gw_.png)

* **Traceroute & ICMP Path Validation:**  
![Routing Logic](./images/proof_split_routing_nva_gw_.png)  
![POC Verification](./images/proof_poc_split_routing.png)

---

## 3. NVA Inspection & Hybrid Identity Proof

Confirms that all authentication and hybrid traffic flows are inspected at Layer 7 by the NVA before reaching the cloud-resident identity services.

* **Palo Alto NVA Flow Logs (On-Prem to Cloud RADIUS):**  
  Proves the NVA is successfully intercepting and permitting authentication traffic from the physical edge.  
![NVA Inspection Logs](./images/logs_onprem_pa-cppmvm1812.png)

* **ClearPass PEAP Authentication Success:**  
  This Access Tracker capture from `CPPM3` (Azure) provides definitive proof of the hybrid identity solution, showing successful **PEAP-MSCHAPv2** authentication for on-premises clients (`lab-wired` service) traversing the Azure Transit Hub.  
![ClearPass PEAP Success](./images/proof_logs_cppm_vm_peap.png)

* **End-to-End Traffic Flow Proof:**  
![General Proof](./images/proof.png)

---

## 4. Supplementary Evidence Logs

Additional supporting logs for deeper validation and troubleshooting:

* **VPN Gateway Spoke Logs:**  
  [./images/2303_logs_vpn_gw_spoke.png](./images/2303_logs_vpn_gw_spoke.png)

* **General Split-Routing Evidence:**  
  [./images/proof_split_routing.png](./images/proof_split_routing.png)

---

## 🔹 Validation Summary

The results confirm that:

- All hybrid authentication flows are successfully inspected and enforced  
- Routing symmetry is preserved across load-balanced NVAs  
- Split-path routing operates as designed without introducing asymmetric drops  
- The architecture enforces a strict "no shortcut" inspection policy  

This validates the design as production-ready for identity-driven hybrid access.

---

[Back to Top](#validation-proof-transit-security-hub-azure) | [Back to Main Architecture](../../README.md)