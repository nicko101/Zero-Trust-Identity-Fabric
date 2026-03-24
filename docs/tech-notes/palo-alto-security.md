$paloContent = @'
# Deep-Dive: Palo Alto Layer-7 Security & L3 Gateway Logic

## 1. Router-on-a-Stick & VLAN Segmentation
The PA-VM acts as the centralized Layer 3 termination point for the on-premises lab, providing granular control over "East-West" traffic between internal segments.

### Logical Interface Mapping
By utilizing a single physical interface (`ethernet1/2`) as an 802.1Q trunk, the firewall provides dedicated gateways and security zones for each segment:
* **Interface `1/2.10` (VLAN 10):** Management / AD Services (`10.0.10.1/24`)
* **Interface `1/2.20` (VLAN 20):** Production Workloads (`10.0.20.1/24`)
* **Interface `1/2.50` (VLAN 50):** Guest Isolation (`10.0.50.1/24`)
* **Interface `1/2.60` (VLAN 60):** Security/Identity Hub (`10.0.60.1/24`)

## 2. IP Address Management (DHCP)
To ensure seamless endpoint onboarding, the Palo Alto handles DHCP services for the lab:
* **Guest & Prod Zones:** The firewall acts as the **DHCP Server**, handing out IPs, DNS, and Gateway settings directly to untrusted or IoT devices.
* **Management Zone:** The firewall utilizes **DHCP Relay** to point requests toward the Windows Domain Controller, ensuring domain-joined assets receive correct DNS suffixes and AD registration.

## 3. Context-Aware Security (User-ID & XML-API)
The firewall moves beyond simple IP-based rules by mapping IP addresses to specific identities (e.g., `nick`).
* **The Handshake:** Upon successful 802.1X authentication, **Aruba ClearPass** sends an **XML-API POST** to the Palo Alto.
* **Result:** The firewall binds the authenticated user to the IP, allowing for policies based on **Active Directory Groups** rather than static subnets.

---
**Navigation**
[Back to Engineering Analysis](../engineering-analysis.md) | [Back to Main Architecture](../../README.md)
'@

$paloContent | Out-File -FilePath ".\palo-alto-security.md" -Encoding utf8 -Force