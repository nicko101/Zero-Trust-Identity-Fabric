# Centralised Configuration Artifacts

This directory serves as the unified backup repository for the Zero Trust Identity Fabric. It contains the raw, machine-readable configuration files, infrastructure-as-code (IaC) templates, and system run-states required for disaster recovery and environment auditing.

---

## 1. Cloud Infrastructure (Azure ARM Templates)
These JSON templates define the logical structure of the Azure environment and support automated resource deployment.

* **RG-NVA-HA.json:** Deployment logic for the High Availability (HA) cluster, including the Azure Standard Load Balancer "sandwich" configuration.
* **RG-PA-NVA.json:** Configuration for the Palo Alto VM-Series Network Virtual Appliances.
* **RG-Spoke1.json:** Infrastructure definition for the Spoke VNet workloads and subnet-level User Defined Routes (UDRs).
* **RG-VPNGW.json:** Configuration for the Azure Virtual Network Gateway used for Site-to-Site hybrid connectivity.

## 2. Security Enforcement (Palo Alto XML Backups)
These XML files are point-in-time configuration snapshots of the firewall policy engine and networking stack.

* **nva_www_gw.xml:** Configuration backup for the Azure-resident VM-Series cluster.
* **onprem_www_gw.xml:** Configuration backup for the on-premises High Availability firewall pair.

## 3. Identity and Policy (ClearPass)
Backups and logs detailing the Policy Decision Point (PDP) configuration and system status.

* **rg_cppm.json:** JSON export of the ClearPass Policy Manager resource group logic and identity provider integrations.
* **sh_run_cppm_vm_w.txt:** Historical CLI run-state log detailing the virtual appliance status and network settings.

---

## Maintenance and Audit
These files should be updated whenever a significant change is made to the production policy or infrastructure. This ensures the repository remains the single source of truth for the entire fabric architecture.

[Back to Top](#centralised-configuration-artifacts) | [Back to Main Architecture](../README.md)