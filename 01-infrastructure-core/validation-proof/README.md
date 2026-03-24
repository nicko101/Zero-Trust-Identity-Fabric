# Validation Proof: Infrastructure Core Dossier

This folder contains the engineering blueprints and technical validation evidence for Lab02, synthesized through automated configuration analysis. This dossier proves the efficacy of the Zero Trust architecture.

## Table of Contents
* [1. Macro Architecture & Strategy](#1-macro-architecture--strategy)
* [2. Pillar 1: Identity & PKI Automation](#2-pillar-1-identity--pki-automation)
* [3. Pillar 2: Network Access Control (NAC)](#3-pillar-2-network-access-control-nac)
* [4. Pillar 3: Perimeter Defense & Routing](#4-perimeter-defense--routing)
* [5. Operational Synthesis & Telemetry](#5-operational-synthesis--telemetry)

---

## 1. Macro Architecture & Strategy
The foundational blueprint illustrating the global integration of Proxmox, Azure VPN, and the identity-driven security fabric.

* **Master Macro-Architecture:** ![Master Blueprint](Zero%20Trust%20Engineering%20Blueprint1.png)
* **Engineering Pillars & Tech Stack:** ![Tech Stack](Zero%20Trust%20Engineering%20Blueprint2.png)

---

## 2. Pillar 1: Identity & PKI Automation
Validation of the SCEP/NDES certificate pipeline, ensuring automated trust anchored by the Microsoft Issuing CA.

* **Automating Trust Flow:** ![PKI Flow](Zero%20Trust%20Engineering%20Blueprint3.png)
* **Proof of Delivery (SCEP in Action):** ![SCEP Health Card](Zero%20Trust%20Engineering%20Blueprint4.png)
* **Infrastructure Baseline:** ![Connector Health](Zero%20Trust%20Engineering%20Blueprint5.png)

---

## 3. Pillar 2: Network Access Control (NAC)
Real-time validation of device compliance and chained authentication using Aruba ClearPass and Microsoft Intune.

* **NAC Authentication Logic:** ![ClearPass Accept](Zero%20Trust%20Engineering%20Blueprint6.png)
* **Authentication Evolution (TEAP):** ![TEAP Rationale](Zero%20Trust%20Engineering%20Blueprint7.png)
* **Anatomy of Compliance Enforcement:** ![Compliance Mapping](Zero%20Trust%20Engineering%20Blueprint8.png)

---

## 4. Pillar 3: Perimeter Defense & Routing
Verification of Palo Alto L7 inspection, split-tunneling efficiency, and secure segmentation for guest/corporate traffic.

* **Perimeter Architecture:** ![Perimeter Blueprint](Zero%20Trust%20Engineering%20Blueprint9.png)
* **Secure Guest Connection (Phase 1):** ![Guest Foundation](Zero%20Trust%20Engineering%20Blueprint10.png)
* **Active Security Inspection (Phase 2):** ![Active Inspection](Zero%20Trust%20Engineering%20Blueprint11.png)
* **Proof of Concept: Split Routing:** ![Split Routing Proof](Zero%20Trust%20Engineering%20Blueprint12.png)

---

## 5. Operational Synthesis & Telemetry
A holistic summary of the system state, demonstrating a stable, authenticated, and inspected environment.

* **The Zero Trust Lifecycle:** ![Lifecycle Flow](Zero%20Trust%20Engineering%20Blueprint13.png)
* **System Telemetry & Wrap-Up:** ![Final Telemetry](Zero%20Trust%20Engineering%20Blueprint14.png)

---

## Navigation
[Back to Infrastructure Index](../README.md) | [Back to Main Architecture](../../README.md)