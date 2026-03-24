# Transit Security Hub (Azure)

This module defines the hybrid transit architecture, routing, and split-routing logic connecting the on-premises data center to Azure. Validation evidence and configuration exports for the transit fabric are centralized in this module-level hub.

---

## Module Structure

* **[Design Standards](./design-standards/)**: Foundational Azure VPN Gateway specs, IPsec parameters, and routing table design.
* **[Implementation Logic](./implementation-logic/)**: Technical configuration details for the split-routing logic and NVA transit routing.
* **[Validation & Evidence](./validation-proof/)**: Logs and engineering blueprints verifying S2S connectivity and traffic steering.

---

## Access Validation-Proof Hub
To view the results of the transit routing and Palo Alto traffic inspection:
**[View Validation Evidence](./validation-proof/README.md)**