# Deep-Dive: Proxmox Networking & VyOS NAT Gateway

## 1. Physical Infrastructure & Hypervisor Mapping
The Proxmox VE host acts as the physical-to-virtual bridge. It utilizes two physical Network Interface Cards (NICs) to isolate administrative traffic from the secure lab environment.

### Interface Configuration
* **Interface 1 (Management):** Connected to the physical `192.168.x.x` home network. This provides the primary IP for the hypervisor (Web GUI/SSH).
* **Interface 2 (Lab Trunk):** Configured as **Linux Bridge `vmbr1`**. 
    * **VLAN Aware:** Enabled.
    * **Role:** Carries the 802.1Q trunk from the physical Aruba CX switch into the virtual environment, supporting VLANs 10, 20, 30, 40, 50, and 60.

## 2. VyOS Edge Gateway & NAT Logic
Because the lab environment operates on a private `10.0.0.0/8` range, a **VyOS Virtual Router** serves as the primary "Internet Gateway."

### The NAT Handshake
To allow internal VMs to reach the internet while preventing the home network from seeing internal lab traffic, VyOS performs **Source NAT (Masquerade)**:
* **Inside Interface (`eth1`):** Sits on the internal trunk; Default Gateway for the lab.
* **Outside Interface (`eth0`):** Receives a DHCP address from the `192.168.x.x` home network.
* **Logic:** Traffic entering `eth1` is translated to the `eth0` IP before exiting.

## 3. On-Premises Palo Alto (PA-VM) Termination
The **On-Premise Palo Alto VM** is the primary security headend:
* **VPN Termination:** Terminates S2S IPsec tunnels from Azure VPN Gateway and Azure NVA.
* **L3 Routing:** Handles inter-VLAN routing for all secure segments.
* **Upstream:** Uses the VyOS gateway as its Next Hop for public internet breakout.

---
**Navigation**
[Back to Engineering Analysis](../engineering-analysis.md) | [Back to Main Architecture](../../README.md)