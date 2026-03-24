# Cloud Networking Design Standards: Infrastructure Core

This document outlines the core components and architectural decisions that build the designed infrastructure for the Zero Trust Identity Fabric.

## 1. Virtualization and Core Routing
The foundation relies on a Proxmox virtual switch and bridge setup, utilizing advanced port tagging to manage strict traffic isolation across the environment.
* Deep Dive: [Proxmox Networking Logic](../../docs/tech-notes/proxmox-networking.md)

To establish a realistic boundary, a VyOS router VM provides Network Address Translation, separating the internal `10.0.0.0/8` network fabric from the physical `192.168.0.0/24` local LAN. Additionally, GNS3 is integrated to provide multi-vendor simulation capabilities, supporting complex CSR automation testing.

## 2. Wired and Wireless Fabric
The wired access edge is simulated using Aruba CX Switch VMs. These virtual switches connect the client VMs and are configured to pass 802.1X EAPOL packets directly to the on-premises ClearPass cluster for identity authentication.
* Deep Dive: [Aruba CX Switching Standards](../../docs/tech-notes/aruba-cx-switching.md)
* Deep Dive: [802.1X and ClearPass Design](../../docs/tech-notes/8021x-clearpass-cx.md)

For the wireless infrastructure, Aruba Central provides cloud-managed orchestration for the Central Site WiFi configurations, ensuring unified policy enforcement across all access points.

## 3. Directory Services
The on-premises Active Directory Domain Controller acts as the core identity provider. It is actively linked to Azure AD to synchronize user objects and support hybrid identity verification across both environments.
* Deep Dive: [Entra App Proxy Architecture](../../docs/tech-notes/entra-app-proxy.md)

## 4. Security Edge and Hybrid Transit
The on-premises Palo Alto firewall is deployed in a High Availability cluster and operates as a Router-on-a-Stick, handling all inter-VLAN routing for the `10.0.0.0/8` networks.
* Deep Dive: [Palo Alto Security Standards](../../docs/tech-notes/palo-alto-security.md)

This firewall forwards all hybrid traffic to the Azure VNets, allowing critical on-premises authentication traffic to reach cloud-hosted endpoints, such as syncing with the CPPM3 node in Azure. This transit relies on two distinct paths: a Site-to-Site tunnel to the Azure VPN Gateway, and a direct Site-to-Site tunnel to the Azure NVA for forced inspection.
* Deep Dive: [Palo Alto Azure Transit](../../docs/tech-notes/palo-azure-transit.md)

---
[Access Validation-Proof Hub](../validation-proof/README.md) | [Back to Parent Category](../README.md) | [Back to Main Lab Architecture](../../README.md)