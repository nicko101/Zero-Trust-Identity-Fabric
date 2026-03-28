# Palo Alto Security and User-ID Integration

This document details the Layer 7 security enforcement and identity-aware policies used to secure the hybrid fabric.

## 1. User-ID and Identity Mapping
To move beyond simple IP-based rules, the firewall utilizes **User-ID** to associate network traffic with specific Active Directory identities.
* **Mechanism:** The firewall parses Active Directory Security Event Logs to map source IPs to usernames (e.g., `lab\nick.f`).
* **Enforcement:** This allows security rules to be scoped to specific AD Groups, ensuring that high-privilege access is restricted to verified identities regardless of their physical location in the lab.

## 2. SSL/TLS Decryption (Forward Proxy)
To prevent threats from hiding inside encrypted streams, the Palo Alto is configured for **SSL Decryption**.
* **PKI Integration:** The firewall utilizes a Subordinate CA certificate issued by the on-premises Microsoft PKI.
* **Inspection:** Because the Issuing CA is trusted by all domain-joined endpoints, the firewall can inspect outbound HTTPS traffic for malicious payloads or unauthorized data exfiltration without causing certificate errors.

## 3. Remote Access: GlobalProtect & Azure SAML
Secure remote connectivity is provided through **GlobalProtect** VPN, utilizing a modern identity-first approach.
* **SAML 2.0 Integration:** Authentication is offloaded to **Azure Entra ID** via a dedicated Enterprise Application. This enables Single Sign-On (SSO) and ensures that GlobalProtect sessions are subject to Azure Conditional Access Policies (MFA).
* **Identity Flow:** Entra ID validates the user, and the Palo Alto receives a SAML assertion to grant access. This provides the firewall with real-time User-ID data for remote clients, ensuring consistent policy enforcement.

## 4. Threat Prevention and Offensive Validation
Every security rule is hardened with **Threat Prevention** profiles to block known vulnerabilities, malware, and spyware.

* **Sandboxing:** Suspicious files are automatically submitted to the **WildFire** cloud for analysis.
* **Offensive Validation (Kali Linux):** The efficacy of these security profiles is validated using an internal **Kali Linux** instance. By executing simulated exploits, credential harvesting, and port scans, the Palo Alto's ability to detect, log, and drop malicious traffic is confirmed and documented.

## 5. NVA Routing and Traffic Engineering (Azure Transit Integration)

The NVAs operate as stateless firewalls (no session synchronisation), requiring strict traffic symmetry enforcement.

In addition to traditional Layer 7 security enforcement, the Palo Alto VM-Series firewalls deployed in Azure act as **Network Virtual Appliances (NVAs)** within the Transit Security Hub.

This introduces advanced routing and traffic engineering requirements beyond standard firewall deployments.

---

### Virtual Router Design

The NVA is segmented into multiple Virtual Routers (VRs) to separate traffic domains:

- **Trust VR**  
  Handles internal and hybrid traffic (Spoke VNets, On-Prem, Identity services such as ClearPass)

- **Untrust VR**  
  Handles internet-bound traffic and NAT egress

This separation ensures deterministic routing and prevents unintended traffic leakage between internal and external domains.

---

### Inter-VR Routing

Traffic is explicitly passed between VRs based on destination:

- Internal traffic enters via the Trust interface  
- Internet-bound traffic is forwarded:
  → Trust VR → Untrust VR  

This design enforces clear security boundaries and avoids implicit routing behaviour.

---

### Route Table Separation

Each VR maintains its own routing logic:

### Route Table Separation

Each VR maintains its own routing logic:

**Trust VR**
- Routes to:
  - Azure Spokes (via Internal Load Balancer VIP acting as next-hop)
  - On-premises networks (via tunnel.100 and VPN Gateway path)
  - Azure Health Probe (`168.63.129.16/32`) for load balancer monitoring
- Default route:
  - `0.0.0.0/0` → `next-vr untrust-vr` (internet-bound traffic)

  ...

**Untrust VR**
- Default route:
  - Internet via external interface  
- Handles NAT and return traffic

**Untrust VR**
- Default route:
  - Internet via external interface  
- Handles NAT and return traffic  

---

### Policy-Based Forwarding (PBF)

PBF is used to explicitly control traffic paths and ensure deterministic routing across the dual-path architecture.
PBF rules take precedence over the routing table, allowing explicit path selection independent of VR route lookup.

Example:

- Internal hybrid traffic (Spoke ↔ On-Prem, including identity flows) is matched by PBF rules
- Traffic is forwarded to the correct next-hop IP (associated with VPN Gateway path or direct NVA tunnel)
- **Enforce Symmetric Return** is enabled to ensure return traffic follows the same path

This prevents asymmetric routing and ensures session consistency in a stateless NVA design.

---

### Relationship to Azure Transit Architecture

This NVA design directly supports the dual-path routing model implemented in the Azure Transit Hub:

- **Internet traffic**  
  → Routed via direct IPSec tunnel to the NVA  
  → Processed through Untrust VR  

- **Internal hybrid traffic (Spoke ↔ On-Prem, Identity flows)**  
  → Routed via VPN Gateway  
  → Redirected to ILB  
  → Inspected via Trust VR  

This ensures:

- Full Layer 7 inspection of all traffic  
- Symmetric routing across stateless NVAs  
- Consistent Zero Trust enforcement across both identity and application flows  

---

### Why This Matters

Without VR separation and PBF:

- Traffic becomes asymmetric  
- Sessions are dropped in stateless designs  
- Inspection becomes inconsistent  

This implementation ensures:

- Deterministic routing  
- Full inspection coverage  
- Scalable and resilient hybrid connectivity  

---

### Internal Source NAT for Symmetric Return

To support the stateless Active/Active design behind the Azure Internal Load Balancer, Source NAT is applied to selected internal transit flows.

- Hybrid traffic (e.g., Spoke ↔ On-Prem) is source-translated to the NVA’s internal (trust) interface IP
- This ensures return traffic is routed back to the same NVA instance via the ILB

Without this:

- Return traffic may be sent to a different NVA
- This results in session drops due to lack of state synchronisation

This mechanism guarantees:

- Flow stickiness per NVA  
- Symmetric routing across stateless firewalls  
- Reliable inspection for all hybrid traffic flows  

---

### Related Architecture Modules

- [Transit Security Hub (Azure)](../../02-transit-security-hub-azure/)  
- [Implementation Logic](../../02-transit-security-hub-azure/implementation-logic/README.md)

---

**Navigation**

[Back to Engineering Analysis](../engineering-analysis.md) | [Back to Main Architecture](../../README.md)