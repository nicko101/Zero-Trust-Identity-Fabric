# Validation Proof: Transit Security Hub (Azure)

This section provides technical evidence validating the Azure Transit Security Hub architecture and its ability to enforce inspected, symmetric, and identity-aware traffic flows across hybrid environments.

---

## Validation Objectives

This section validates that the Azure Transit Security Hub meets the core architectural guarantees:

- **Symmetric Routing**  
  All flows return via the same NVA instance to preserve session state  

- **Full Traffic Inspection**  
  No hybrid or inter-zone traffic bypasses the NVA layer  

- **Hybrid Identity Reachability**  
  On-premises authentication traffic successfully reaches the cloud-resident ClearPass node  

- **Split-Path Routing Behaviour**  
  Internet and internal traffic follow distinct, controlled paths  

- **High Availability Behaviour**  
  Traffic continues to flow correctly across load-balanced NVAs  

Each validation artefact below maps directly to these guarantees.

---

## 1. Split-Routing Validation (Dual-Path Behaviour)

Validates that traffic is correctly separated between:

- Direct NVA tunnel (internet-bound traffic)  
- VPN Gateway path (internal hybrid traffic)  

This confirms resolution of IPSec tunnel pinning and controlled traffic steering.

* **Split-Routing Logic Analysis:**  
![Split Routing Analysis](./images/proof_split_routing.png)

* **NVA vs VPN Gateway Pathing:**  
![NVA vs Gateway Pathing](./images/proof_split_routing_nva_gw_.png)

* **Traceroute & ICMP Validation:**  
![POC Verification](./images/proof_poc_split_routing.png)

---

## 2. NVA Inspection & Traffic Enforcement

Confirms that all hybrid traffic is inspected at the NVA layer before reaching Azure workloads or identity services.

* **Palo Alto NVA Flow Logs (RADIUS / Hybrid Traffic):**  
  Demonstrates that authentication and hybrid flows are intercepted and processed by the NVA.  
![NVA Inspection Logs](./images/logs_onprem_pa-cppmvm1812.png)

---

## 3. Hybrid Identity Validation (ClearPass in Azure)

Validates that on-premises authentication traffic successfully reaches the cloud-resident Policy Decision Point (ClearPass).

* **ClearPass Authentication Success (Access Tracker):**  
  Confirms successful **PEAP-MSCHAPv2 authentication** for on-premises clients via the Azure Transit Hub.  
![ClearPass PEAP Success](./images/proof_logs_cppm_vm_peap.png)

This proves:

- Identity traffic traverses the VPN Gateway path  
- Traffic is inspected before reaching ClearPass  
- Policy decisions are enforced in the cloud  

---

## 4. Supplementary Evidence

Additional supporting logs for troubleshooting and deeper validation:

* [VPN Gateway Logs](./images/2303_logs_vpn_gw_spoke.png)  
* [Additional Split-Routing Evidence](./images/proof_split_routing_.png)

---

## Validation Summary

The results confirm that:

- All hybrid authentication flows are inspected and enforced  
- Routing symmetry is preserved across load-balanced NVAs  
- Split-path routing operates correctly without asymmetric drops  
- No traffic bypasses the inspection layer  

This validates the architecture as a production-ready Zero Trust hybrid access model.

---

[Back to Top](#validation-proof-transit-security-hub-azure) | [Back to Main Architecture](../../README.md)