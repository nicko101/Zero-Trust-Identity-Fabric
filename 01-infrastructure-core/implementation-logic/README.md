# implementation logic: infrastructure core

this directory documents the as-built configuration and technical logic for the foundation of the zero trust identity fabric. it details how the virtualization, identity, and switching layers are integrated into a functional security ecosystem.

---

## 1. hypervisor deployment (proxmox ve)
the infrastructure is anchored on a **proxmox ve** hypervisor to provide a hardware-agnostic, snapshot-capable environment for security testing.

* **logic:** proxmox hosts the core domain controller, the clearpass policy decision point, and the palo alto nva. this allows for rapid rollbacks during iterative policy testing and ensures consistent resource allocation across the fabric.
* **deep dive:** [proxmox networking logic](../../docs/tech-notes/proxmox-networking.md)

## 2. wired fabric orchestration (aruba cx and netedit)
the wired edge is comprised of **arubaos-cx** switches, managed through a central orchestration engine.

* **logic:** **netedit** is utilized as the primary automation engine. beyond monitoring, it is used to execute **python-based scripts** that push standardized aaa/radius and vlan configurations. this ensures that every switch port adheres to the zero trust enforcement model without manual entry errors.
* **deep dive:** [aruba cx switching and orchestration](../../docs/tech-notes/aruba-cx-switching.md)

## 3. policy decision point (clearpass)
**aruba clearpass (cppm)** serves as the policy decision point (pdp) for the entire fabric.

* **logic:** clearpass is deployed as a virtual appliance with isolated management and data planes. its implementation centers on the **intune extension** and **graph api** integration, which allows the engine to verify device compliance status before granting network access.
* **deep dive:** [clearpass advanced services](../../docs/tech-notes/clearpass-advanced-services.md) | [802.1x integration](../../docs/tech-notes/8021x-clearpass-cx.md)

## 4. secure tunnel and trust lifecycle (app proxy and scep)
this module automates the delivery of identity certificates without requiring inbound firewall rules.

* **logic:** the **microsoft entra private network connector (app proxy)** establishes an outbound-only https tunnel. this enables **microsoft intune** to communicate with the internal **ndes** server. certificates are then issued via **scep**, creating a secure, automated trust loop for all managed endpoints.
* **deep dive:** [entra app proxy](../../docs/tech-notes/entra-app-proxy.md) | [pki scep lifecycle](../../docs/tech-notes/pki-scep-lifecycle.md)

## 5. security enforcement edge (palo alto nva)
the **palo alto vm-series** provides layer 7 inspection and serves as the transit security hub.

* **logic:** the nva is implemented as the primary gateway for all inter-zone and hybrid-cloud traffic. it utilizes **user-id** to ingest identity data from clearpass, allowing security policies to be written based on users and groups rather than static ip addresses.
* **deep dive:** [palo alto security logic](../../docs/tech-notes/palo-alto-security.md)

## 6. observability and validation
continuous monitoring and security validation are integrated into the core fabric.

* **logic:** the environment is instrumented with centralized logging for real-time observability. the fabric is then subjected to **offensive security simulations** to validate that the zero trust policies are effectively blocking unauthorized lateral movement.
* **deep dive:** [secops and observability](../../docs/tech-notes/secops-siem-observability.md) | [offensive validation](../../docs/tech-notes/pentesting-offensive-validation.md)

---

## navigation
[back to top](#implementation-logic-infrastructure-core) | [back to main architecture](../../README.md)