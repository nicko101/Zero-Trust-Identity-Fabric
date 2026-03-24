$guestContent = @'
# Deep-Dive: Secure Guest WiFi Anatomy

## 1. Phase 1: The Foundation (VLAN 50 Isolation)
The Guest network is built on a "Zero-Trust" principle. 
* **Layer 2 Isolation:** Guest traffic is tagged on **VLAN 50** from the AP to the Palo Alto.
* **Zone Security:** The Palo Alto treats the Guest Zone as "Untrusted," strictly blocking any traffic destined for the `10.0.0.0/8` internal range via a "Drop-All-Internal" security policy.

## 2. Phase 2: The Action (Self-Registration Portal)
Instead of a shared PSK, guests utilize a **Captive Portal** hosted on **Aruba ClearPass**.
* **Self-Registration:** Guests enter their details (Name/Email) and receive temporary credentials.
* **MAC Caching:** ClearPass remembers the device's MAC address for 24 hours, providing a seamless "re-connect" experience without requiring a second login.

---
**Navigation**
[Back to Engineering Analysis](../engineering-analysis.md) | [Back to Main Architecture](../../README.md)
'@

$guestContent | Out-File -FilePath ".\guest-wifi-anatomy.md" -Encoding utf8 -Force