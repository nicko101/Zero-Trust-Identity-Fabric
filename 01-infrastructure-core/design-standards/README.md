# design standards: infrastructure core

this section defines the architectural requirements and theoretical standards for the zero trust identity fabric foundation.

## 1. virtualization strategy (proxmox ve)
* **architecture:** the design specifies a type-1 hypervisor configuration to ensure that policy decision points (pdp) remain available.
* **deep dive:** [proxmox networking logic](../../docs/tech-notes/proxmox-networking.md)

## 2. network topology (aruba cx)
* **topology logic:** utilizes a spine-leaf architecture to provide non-blocking throughput.
* **vlan segmentation:** standardized vlan schema (v5-v200) to isolate management and user data.
* **deep dive:** [aruba cx switching standards](../../docs/tech-notes/aruba-cx-switching.md)

## 3. identity policy design (clearpass)
* **pdp selection:** clearpass is designated as the central pdp, ingesting multi-vendor telemetry and cloud compliance.
* **deep dive:** [802.1x and clearpass design](../../docs/tech-notes/8021x-clearpass-cx.md)

## 4. secure access strategy (entra app proxy)
* **outbound-only strategy:** eliminates inbound firewall holes by using an outbound-only https tunnel for scep traffic.
* **deep dive:** [entra app proxy architecture](../../docs/tech-notes/entra-app-proxy.md)

## 5. security edge design (palo alto)
* **nva role:** the palo alto vm-series is designated as the network virtual appliance (nva) for north-south inspection.
* **deep dive:** [palo alto security standards](../../docs/tech-notes/palo-alto-security.md)

[back to top](#design-standards-infrastructure-core) | [back to main architecture](../../README.md)