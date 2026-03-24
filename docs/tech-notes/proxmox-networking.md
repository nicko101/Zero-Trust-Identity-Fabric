# Proxmox Networking and Hypervisor Logic

This document details the virtualization foundation and the specific kernel-level tuning required to support high-fidelity enterprise networking, 802.1X authentication, and symmetric routing.

## 1. Physical Infrastructure & Hypervisor Mapping
The Proxmox VE host acts as the physical-to-virtual bridge, utilizing dedicated Network Interface Cards (NICs) to isolate administrative traffic from the secure lab environment.

### Interface Configuration
* **Management Interface:** Connected to the physical network for hypervisor administration (Web GUI/SSH).
* **Lab Trunk (vmbr1/vmbr22):** A VLAN-aware Linux bridge that carries the 802.1Q trunk. This supports the segmented architecture for VLANs 10, 20, 30, 40, 50, and 60.

## 2. Advanced EAPOL Forwarding (802.1X Support)
A standard Linux bridge acts as a MAC-layer proxy and typically consumes or drops IEEE 802.1X reserved group MAC frames. To allow **EAPOL (Extensible Authentication Protocol over LAN)** packets to pass from the endpoints to the virtual ArubaOS-CX switch and ClearPass cluster, a specific kernel mask is applied.

### The group_fwd_mask Tweak
The bridge is configured to forward restricted L2 frames rather than terminating them at the hypervisor layer.

**Kernel Configuration (/etc/network/interfaces):**
auto vmbr22
iface vmbr22 inet manual
        bridge-ports none
        bridge-stp off
        bridge-fd 0
        post-up /bin/sh -c 'echo 8 > /sys/class/net/$IFACE/bridge/group_fwd_mask'

* **The Logic:** Setting the mask to **8** (binary **1000**) instructs the bridge to forward frames addressed to the **01:80:c2:00:00:03** multicast group. This is the critical "enabler" for virtualized 802.1X/TEAP environments, ensuring authentication frames reach the Policy Enforcement Point (PEP).

## 3. VyOS Edge Gateway & NAT Logic
To provide internet access to the private 10.0.0.0/8 lab range without exposing it to the home network, a VyOS Virtual Router performs Source NAT (Masquerade).

* **Inside Interface (eth1):** Acts as the primary Internet Gateway for the lab.
* **Outside Interface (eth0):** Receives a DHCP address from the upstream physical router.
* **NAT Handshake:** Traffic entering the lab fabric is translated to the VyOS external IP before exiting, providing a clean security boundary.

## 4. Compute High Availability (HA)
The Proxmox cluster supports the high-availability requirements of the ZTNA stack:
* **Palo Alto HA:** Deployed as an Active/Passive pair with dedicated virtual NICs for HA1 (Control) and HA2 (Data) synchronization.
* **ClearPass Publisher/Subscriber:** Deployed in a clustered configuration to ensure the Policy Decision Point (PDP) remains reachable during hypervisor maintenance.

## 5. Hardware-Assisted Nested Virtualization
To support the ArubaOS-CX virtual appliances and the GlobalProtect VPN throughput requirements, the CPU type is set to **host**. This passes through Intel VMX / AMD-V instructions, allowing the nested GNS3 environment to run with near-native hardware acceleration.

---

[Back to Engineering Analysis](../engineering-analysis.md)