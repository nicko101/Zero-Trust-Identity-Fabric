# Centralized Configuration Artifacts

This directory serves as the unified backup repository for the Zero Trust Identity Fabric. It contains the raw configuration files, infrastructure-as-code (IaC) templates, and system run-states organized by technology stack.

---

## 1. [Aruba-CX](./aruba-cx/)
Aruba AOS-CX CLI configuration files and Port-Access policy exports for the physical network edge.

## 2. [Azure Templates](./azure-templates/)
JSON templates defining the logical structure of the Azure environment, including the NVA HA cluster, VPN Gateways, and Spoke VNets.

## 3. [ClearPass](./clearpass/)
Policy Decision Point (PDP) configuration exports, identity provider integrations, and virtual appliance run-state logs.

## 4. [Palo Alto](./palo-alto/)
Point-in-time XML configuration snapshots for both the Azure-resident VM-Series cluster and the on-premises HA firewall pair.

---

## Maintenance and Audit
These files are the single source of truth for the fabric architecture. Update these artifacts following any production change to ensure disaster recovery readiness and audit compliance.

[Back to Main Lab Architecture](../README.md)