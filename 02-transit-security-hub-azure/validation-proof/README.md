# Validation Proof: Transit Security Hub (Azure)

This folder contains the technical evidence and engineering blueprints validating the Multi-Tunnel Transit Hub and the Split-Routing architectural solution between On-Premises and Azure.

---

## 1. Hybrid Connectivity & Macro Architecture
Confirmation of the established S2S IPsec tunnels and the global integration between the Proxmox DC and the Azure environment.

* **Hybrid Master Blueprint:**
![Master Architecture Blueprint](../../01-infrastructure-core/validation-proof/images/Zero%20Trust%20Engineering%20Blueprint1.png)

* **S2S Tunnel Status:**
![Connection Proof](./images/proof_connection.png)

* **BGP Adjacency (VPNGW):**
![VPNGW BGP](./images/palo-azure_vpngw_bgp.png)

---

## 2. Split-Routing Verification (Validated Solution)
Technical proof of the split-routing logic, demonstrating traffic steering across separate paths for management and high-security NVA breakout.

* **Split-Routing Logic Analysis:**
![Split Routing Analysis](../../01-infrastructure-core/validation-proof/images/Zero%20Trust%20Engineering%20Blueprint12.png)

* **NVA Pathing Verification:**
![NVA vs Gateway Pathing](./images/proof_split_routing_nva_gw_.png)

* **Traceroute & ICMP Path Validation:**
![Routing Logic](./images/proof_split_routing_nva_gw_.png)
![POC Verification](./images/proof_poc_split_routing.png)

---

## 3. NVA Inspection & Hybrid Identity Proof
Validation that traffic hitting Azure Spokes, and critical hybrid identity flows, are successfully steered through the NVA for inspection.

* **Palo Alto NVA Flow Logs (On-Prem to Cloud RADIUS):** Proves the NVA is successfully intercepting and permitting the authentication traffic from the physical edge.
![NVA Inspection Logs](./images/logs_onprem_pa-cppmvm1812.png)

* **ClearPass PEAP Authentication Success:** This high-detail Access Tracker capture from `CPPM3` (Azure) is the definitive proof of the hybrid solution. It shows successful **PEAP-MSCHAPv2** authentication for on-premises clients (`lab-wired` service) traversing the Azure Transit Hub.
![ClearPass PEAP Success](./images/proof_logs_cppm_vm_peap.png)

* **End-to-End Traffic Flow Proof:**
![General Proof](./images/proof.png)

---

## 4. Supplementary Evidence Logs
* **VPN Gateway Spoke Logs:** [./images/2303_logs_vpn_gw_spoke.png](./images/2303_logs_vpn_gw_spoke.png)
* **General Split-Routing Evidence:** [./images/proof_split_routing.png](./images/proof_split_routing.png)