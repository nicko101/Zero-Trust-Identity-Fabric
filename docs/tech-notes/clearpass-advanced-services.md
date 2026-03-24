# clearpass policy manager: services and hybrid pki

This document details the configuration of the aruba clearpass policy manager (cppm), focusing on the hybrid certificate strategy and the multi-stage service logic used to enforce zero trust across the fabric.

## 1. hybrid pki and trust architecture
The lab utilizes a two-tier certificate strategy to balance granular internal security with seamless automated orchestration.

* **internal pki (radius/eap-tls):** workstation and user identity certificates are issued by the internal microsoft ca.
    * **benefit:** ensures non-exportable, tpm-backed keys for managed endpoints. since these devices are domain-joined, they trust the internal root natively.
* **public pki (https/dur/guest):** the clearpass web and radius-server certificate for `cppm.duckdns.org` is issued by **let's encrypt**.
    * **benefit:** because let's encrypt is a public authority, arubaos-cx switches and guest devices trust the portal automatically. this enables seamless **downloadable user roles (dur)** and removes "insecure connection" warnings on the guest captive portal.

## 2. core authentication: teap and intune compliance
The primary service for managed endpoints utilizes **tunnelled eap (teap)** to verify both the machine and the user in a single transaction.

![clearpass teap service](../../03-identity-policy-engine/validation-proof/cppm_service_teap.png)

ClearPass acts as the critical bridge between the local network and the azure cloud for real-time compliance validation.

![intune compliance check](../../03-identity-policy-engine/validation-proof/proof-cppm-intune-compliance.png)

* **mechanism:** during the handshake, clearpass queries the **microsoft graph api** for the device's compliant status.
* **decision:** if intune reports the device as non-compliant, clearpass overrides the standard role and assigns a quarantine role, blocking access to the internal `10.0.0.0/8` range.

## 3. advanced services: onboard and onguard
To support a diverse device ecosystem, clearpass provides specialized onboarding and health-check workflows.

* **clearpass onboard (byod):** provides a self-service portal for non-managed or personal devices. it acts as an internal ca to issue unique device certificates, removing the reliance on weak credentials or psks.
* **clearpass onguard (posture):** the onguard agent performs real-time health checks (antivirus status, disk encryption, patch levels). if a device fails health checks, clearpass sends a **radius coa** to the switch to dynamically isolate the port.

## 4. guest access and mac caching (vlan 50)
The guest lifecycle is fully automated to provide secure access with minimal administrative overhead.

* **self-registration:** guests connect to the 'lab' ssid and are redirected to the `cppm.duckdns.org` registration portal.
* **mac caching:** once registered, clearpass caches the device mac address for 24 hours. returning guests are identified via mac-auth and granted access automatically, bypassing the portal while their session is valid.
* **isolation:** clearpass instructs the infrastructure to place these devices in **vlan 50**, where the palo alto nva enforces strict internet-only outbound policies.

---

[back to engineering analysis](../engineering-analysis.md)