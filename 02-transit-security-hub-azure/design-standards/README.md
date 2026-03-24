# Design Standards: Transit Security Hub (Azure)

## Table of Contents
* [1. Azure IP Schema](#1-azure-ip-schema-172x0016)
* [2. Dual-Tunnel Solution](#2-the-bypassed-tunnel-architectural-solution)
* [3. Traffic Steering & Routing](#3-traffic-steering--routing-standards)
* [4. Security Zone Definitions](#4-security-zone-definitions)

---

## 1. Azure IP Schema (172.x.0.0/16)
| VNet | Purpose | CIDR |
| :--- | :--- | :--- |
| **nf-vnet-vpngw** | VPN Gateway Hub | 172.16.0.0/16 |
| **nf-vnet-palo-nva** | Central Security Hub | 172.20.0.0/16 |
| **nf-vnet-spoke** | Workload Spoke | 172.19.0.0/16 |

---

## 2. The Bypassed-Tunnel Architectural Solution
A **Validated Solution** for standalone NVA deployments to separate traffic types:

* **Secure Internet Breakout (Tunnel 300):** On-Prem <-> Palo Alto NVA. Handles all `0.0.0.0/0` traffic for L7 Inspection and User-ID.
* **Internal Backbone (Tunnel 200):** On-Prem <-> Azure VPN Gateway. Handles East-West traffic (AD, DNS) to reduce NVA load.

---

## 3. Traffic Steering & Routing Standards
* **UDRs:** Attached to `nf-vnet-spoke` pointing to the NVA Trust IP.
* **Forced Tunneling:** All spoke internet traffic is backhauled through the NVA.
* **Symmetric PBF:** Ensures return-path symmetry for stateful inspection.

---

## 4. Security Zone Definitions
* **Zone-Trust:** Azure Spoke & On-Prem internal.
* **Zone-Untrust:** Internet egress.
* **Zone-VPN:** GlobalProtect remote users.

---

**Navigation**
[Back to Parent Category](../) | [Back to Main Architecture](../../README.md)