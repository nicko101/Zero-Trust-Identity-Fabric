# Validation Proof: Transit Security Hub (Azure)

This folder contains the technical evidence and engineering blueprints validating the Multi-Tunnel Transit Hub and the Split-Routing architectural solution between On-Premises and Azure.

## Table of Contents
* [1. Hybrid Connectivity & Macro Architecture](#1-hybrid-connectivity--macro-architecture)
* [2. Split-Routing Verification (Validated Solution)](#3-split-routing-verification-validated-solution)
* [3. NVA Inspection & Flow Evidence](#4-nva-inspection--flow-evidence)

---

## 1. Hybrid Connectivity & Macro Architecture
Confirmation of the established S2S IPsec tunnels and the global integration between the Proxmox DC and the Azure environment.

* **Hybrid Master Blueprint:** ![Master Architecture Blueprint](../../01-infrastructure-core/validation-proof/Zero%20Trust%20Engineering%20Blueprint1.png)
* **S2S Tunnel Status:** ![Connection Proof](proof_connection.png)

---

## 2. Split-Routing Verification (Validated Solution)
Technical proof of the split-routing logic, demonstrating traffic steering across separate paths for management and high-security NVA breakout.

* **Split-Routing Logic Analysis:** ![Split Routing Analysis](../../01-infrastructure-core/validation-proof/Zero%20Trust%20Engineering%20Blueprint12.png)
* **NVA Pathing Verification:** ![NVA vs Gateway Pathing](proof_split_routing_nva_gw_.png)
* **Traceroute Validation:** ![Routing Logic](proof_split_routing_.png)

---

## 3. NVA Inspection & Flow Evidence
Validation that traffic hitting Azure Spokes is successfully backhauled to the Palo Alto NVA for Layer 7 inspection and User-ID mapping.

* **Palo Alto NVA Flow Logs:** ![NVA Inspection Logs](logs_onprem_pa-cppmvm1812.png)
* **End-to-End Traffic Flow Proof:** ![General Proof](proof.png)

---

## Navigation
[Back to Parent Category](../README.md) | [Back to Main Architecture](../../README.md)